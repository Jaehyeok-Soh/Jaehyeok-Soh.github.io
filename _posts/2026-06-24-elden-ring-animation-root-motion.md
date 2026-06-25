---
title: 스켈레탈 애니메이션과 Root Motion 구현
date: 2026-06-24
layout: single
permalink: /portfolio/elden-ring/animation-root-motion/
category: implementation
project: 엘든링 모작 및 서버 연동
project_page: /portfolio/elden-ring-recreate/
portfolio_post: true
excerpt: DirectX 11 클라이언트에서 Bone Weight 변환, 애니메이션 스키닝과 Root Motion 기반 이동을 구현한 과정을 정리했습니다.
tags:
  - C++
  - DirectX11
  - Skeletal Animation
  - Root Motion
toc: true
toc_label: 목차
toc_sticky: true
---

[← 엘든링 프로젝트 종합 페이지로 돌아가기]({{ page.project_page | relative_url }})

## 구현 목표

이동과 공격 애니메이션이 전진하는데 캐릭터 Transform이 제자리에 있으면 모델, 충돌 위치, 공격 사거리가 서로 어긋납니다.

이를 해결하기 위해 외부 모델의 Bone Weight를 프로젝트 Vertex 데이터로 변환하고, 애니메이션 스키닝 결과와 Root Bone 이동량을 실제 캐릭터 이동에 연결했습니다.

```text
Bone Index / Weight 변환
→ 애니메이션 스키닝
→ Root Bone Translation Delta 계산
→ 캐릭터 월드 축으로 변환
→ Navigation 검사
→ Transform 위치 갱신
```

## 핵심 코드 1. Bone Weight 변환

**파일:** `ResourceTool/Private/Converter.cpp`  
**역할:** Assimp Mesh의 Bone Weight를 프로젝트 Vertex의 Blend Index와 Weight로 변환합니다.

```cpp
for (size_t b = 0;
	b < srcMesh->mNumBones;
	++b)
{
	aiBone* srcBone =
		srcMesh->mBones[b];

	_int boneIndex =
		GetBoneIndex(
			srcBone->mName.C_Str());

	if (boneIndex < 0)
		continue;

	_float4x4 offset{};
	memcpy(
		&offset,
		&srcBone->mOffsetMatrix,
		sizeof(_float4x4));

	XMStoreFloat4x4(
		&offset,
		XMMatrixTranspose(
			XMLoadFloat4x4(&offset)));

	m_Bones[boneIndex]->offset = offset;

	for (size_t w = 0;
		w < srcBone->mNumWeights;
		++w)
	{
		_int vertexIndex =
			srcBone->mWeights[w].mVertexId;

		_float weight =
			srcBone->mWeights[w].mWeight;

		tempVertexBoneWeights[vertexIndex]
			.AddWeights(
				boneIndex,
				weight);
	}
}

for (size_t v = 0;
	v < tempVertexBoneWeights.size();
	++v)
{
	tempVertexBoneWeights[v].Normalize();

	auto blend =
		tempVertexBoneWeights[v]
			.GetBlendWeight();

	mesh->vertices[v].vBlendIndices =
		blend.indices;

	mesh->vertices[v].vBlendWeight =
		blend.weights;
}
```

Bone Name으로 프로젝트 Bone Index를 찾고, 각 Vertex에 영향을 주는 Bone Index와 Weight를 저장했습니다. Weight는 정규화해 스키닝 입력으로 사용합니다.

[GitHub에서 전체 코드 보기](https://github.com/Jaehyeok-Soh/3dsolo/blob/0d7545ce6cdc7de51b4c3541d65d9234056ed91a/ResourceTool/Private/Converter.cpp#L244-L274)

## 애니메이션 스키닝 연결

변환 단계에서 생성한 Blend Index와 Weight는 애니메이션 프레임 데이터, Bone Offset과 함께 스키닝 계산에 사용했습니다. 이 글에서는 공개 저장소에서 바로 확인할 수 있는 리소스 변환 코드와 Root Motion 적용 코드를 중심으로 설명합니다.

## 핵심 코드 2. Root Motion 이동 적용

**파일:** `Client/Private/StateMove.cpp`  
**역할:** 이전·현재 프레임의 Root Translation 차이를 캐릭터 월드 축 이동으로 변환합니다.

```cpp
auto modelObj = m_pOwner->Find_PartObject(L"Part_Body");
auto RootBoneName = modelObj->GetModel()->GetBoneByIndex(2)->GetName();

auto animation = modelObj->GetModel()->GetAnimationByIndex(m_iAnimIndex);
auto keyFrame = animation->GetKeyframe(RootBoneName);
auto keyFrameSize = keyFrame->GetTransforms().size();
auto currFrame = modelObj->GetAnimator()->GetCurFrame();

if (currFrame == 0 || keyFrameSize - 1 < currFrame)
	return;

auto beforeTransform = keyFrame->GetTransform(currFrame - 1);
auto currentTransform = keyFrame->GetTransform(currFrame);

// ...

float localRight = currentTransform.m_vTranslation.x - beforeTransform.m_vTranslation.x;
float localUp = currentTransform.m_vTranslation.y - beforeTransform.m_vTranslation.y;
float localForward = currentTransform.m_vTranslation.z - beforeTransform.m_vTranslation.z;

_vector ownerPos = m_pOwner->GetTransform().Get_State(STATE::POSITION);
_vector ownerRight = XMVector3Normalize(m_pOwner->GetTransform().Get_State(STATE::RIGHT));
_vector ownerUp = XMVector3Normalize(m_pOwner->GetTransform().Get_State(STATE::UP));
_vector ownerLook = XMVector3Normalize(m_pOwner->GetTransform().Get_State(STATE::LOOK));

_vector moveDistance = ownerRight * localRight + ownerUp * localUp + ownerLook * localForward;

_vector destPos = XMVectorAdd(ownerPos, moveDistance);

if (m_pOwner->GetNavigation()->isMove(destPos))
	m_pOwner->GetTransform().Set_State(STATE::POSITION, destPos);
```

Root Bone의 로컬 이동량을 캐릭터의 Right·Up·Look 축으로 변환했습니다. 최종 위치는 Navigation의 이동 가능 검사를 통과한 경우에만 적용해 애니메이션 이동과 충돌 가능한 위치를 연결했습니다.

[GitHub에서 전체 코드 보기](https://github.com/Jaehyeok-Soh/3dsolo/blob/0d7545ce6cdc7de51b4c3541d65d9234056ed91a/Client/Private/StateMove.cpp#L62-L97)

## 좌표계와 행렬 처리

외부 리소스와 런타임의 좌표계가 다르면 캐릭터 방향, Bone Transform, Root Motion 축이 함께 어긋납니다.

프로젝트에서는 다음 지점을 일관되게 맞추는 데 집중했습니다.

- Assimp Offset Matrix의 Transpose 위치
- Bone Offset과 Animated Transform의 곱 순서
- 모델의 Forward/Up Axis
- CPU와 HLSL의 행렬 규칙
- Root Translation을 캐릭터 월드 축으로 변환하는 순서

## 구현 결과

- Assimp Bone Weight를 프로젝트 Vertex 형식으로 변환했습니다.
- Bone Offset과 Weight를 반영한 애니메이션 스키닝을 구성했습니다.
- Root Bone의 프레임 간 이동량을 실제 Transform 이동으로 연결했습니다.
- Navigation 검사 후 위치를 반영해 애니메이션 이동과 이동 가능 영역을 연결했습니다.

## 현재 한계

- Root Motion 기준 Bone이 Index `2`로 고정되어 있습니다.
- Clip별 Root Motion 사용 여부와 루프 보정이 데이터화되어 있지 않습니다.
- 상태 전환 시 Root 기준값을 일반적으로 초기화하는 구조가 부족합니다.
- 원격 플레이어는 수신 위치를 즉시 적용하며 Snapshot 보간이 없습니다.

## 개선 방향

- Root Motion 대상 Bone과 Clip 정책을 Metadata로 관리합니다.
- 루프 경계와 상태 전환 시 Root 기준값을 초기화합니다.
- 누락 Bone과 Weight 합계를 ResourceTool에서 검증합니다.
- 원격 플레이어에 Snapshot Buffer와 보간을 적용합니다.

## 관련 링크

- [엘든링 프로젝트 종합 페이지]({{ page.project_page | relative_url }})
- [UDP 상태 공유 프로토타입]({{ '/portfolio/elden-ring/udp-sync/' | relative_url }})
- [클라이언트 GitHub](https://github.com/Jaehyeok-Soh/3dsolo)
- [플레이 영상](https://youtu.be/6J3sDV4hN_8)
