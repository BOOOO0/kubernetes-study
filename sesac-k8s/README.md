# 쿠버네티스 아키텍처

- ![image](./img/components-of-kubernetes.svg)

- 컨트롤 플레인은 마스터 노드가 속하는 부분이고 데이터 플레인은 각 워커 노드들의 모음이다.

- 컨트롤 플레인의 api 서버가 노드를 관리하는 중추 역할을 한다. 그런데 api 서버가 명령을 내리는 것이 아니라 워커 노드의 kubelet이 api 서버에 주기적으로 요청을 보내고 etcd에 저장된 사용자가 작성한 yaml 파일 기반의 KVS(key:value로 저장된 NoSQL)에 기반해서 워커 노드의 요청에 응답한다.

- 스케줄러가 노드의 상태를 판단해서 pod를 배치한다.

- 컨트롤러 매니저는 일반 컨트롤 매니저와 클라우드 컨트롤러 매니저가 있는데 EKS, AKS, GKE 같은 서비스를 사용할때 클라우드 컨트롤 매니저를 사용한다.

- 노드 컨트롤러 - 노드가 다운되었을때의 복구를 담당한다.

- 레플리케이션 컨트롤러 - 사용자의 desired state를 유지하기 위해 pod 수를 유지한다.

- 엔드포인트 컨트롤러 - 외부에서 네트워크를 통해 접속할 수 있도록 네트워크 서비스와 파드를 연결시키고 엔드포인트를 생성한다.

- 서비스 어카운트 & 토큰 컨트롤러 - id/pw 기반으로 계정에 대한 접근 권한을 제어한다.

- 클라우드 컨트롤러 매니저의 추가적인 기능은 쿠버네티스 서비스 배포시 로드밸런서와 그 엔드포인트에 대한 public ip 부여(서비스 컨트롤러), 그 서비스에 접근할 수 있도록 네트워크 구성을 수정해주는 라우트 컨트롤러, 오토 스케일링의 제공이다.

- 각 노트에는 컨테이너 런타임이 설치되는데 docker, containerd, rkt 등이 있다. 지금은 containerd가 쿠버네티스 런타임의 기본값인 듯 하다.

- kubelet은 워커 노드에 설치된 에이전트로 볼 수 있다. pod spec에 맞게 컨테이너를 관리한다. 쿠버네티스 명령으로 생성되지 않은 컨테이너는 관리할 수 없다.

- kube-proxy는 포워딩, 네트워크 프록시 등 외부에서 노드로 접근할 수 있도록 라우터의 역할을 하는 컴포넌트이다.

# 쿠버네티스 설치

- `kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml` - flannel은 실습 용도로 보안이 구현이 안된 가벼운 쿠버네티스 네트워크

- `kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.27.0/manifests/calico.yaml` - 실무 용도의 CNI

- 아래 세줄이 자격 증명이다 마스터 노드에 한다. 마스터 노드로서 api 사용 등 그런걸 가능하게 하는듯하다.

```bash
mkdir -p $HOME/.kube

cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

chown $(id -u):$(id -g) $HOME/.kube/config
```

- admin.conf에 key가 담겨있는데 그걸 옮기는 것 이건 다른 VM에서도 설정하면 이 마스터 노드를 사용할 수도 있다는 것 같다.
