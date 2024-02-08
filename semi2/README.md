# Semi 2

- MS Azure

- Master VM - Standard B2s (cpu 2, RAM 4GB)

- Worker VM - Standard B1ms (cpu 1, RAM 2GB)

- 무료 구독 Quota 문제로 총 cpu 코어 4개가 최대이기 때문에 2개의 worker 노드 사용

- Docker를 런타임으로하는 쿠버네티스 1.19.1 버전에서의 실습(설치, 워커 노드 연결, Pod 배포, Service 배포)를 진행한 다음

- AKS 클러스터를 CLI로 구축하고 Pod 배포, Service 배포

- 구축과 설치 과정 영상 남긴 다음 전체 개요 설명

- 리전은 중동 리전을 선택하려고 했으나 마찬가지로 Quota 문제 발생, 무료 구독에서 사용할 수 없는 인스턴스 타입만 있기 때문

- 미국 서부로 결정 (US West 3)

