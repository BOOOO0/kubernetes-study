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

- kubelet은 워커 노드에 설치된 에이전트로 볼 수 있다. pod spec에 맞게 컨테이너를 관리한다. 쿠버네티스 명령으로 생성되지 않은 컨테이너는 관리할 수 없다. 마스터 노드도 kubelet을 가지고 있다. 1.19.1 버전을 설치할 때 마스터 노드에서 kubelet을 실행한 다음 kubeadm을 init 한다. VM에서 설치된 채로 복사하기 위해서 사용한 것인지 잘 모르겠다. 일단 이 부분은 자세히 알 수 없으니 마스터와 워커 모두 kubelet, kubeadm을 설치한다.

- Master Node의 쿠버네티스 컴포넌트들은 모두 하나의 Pod로 컨테이너에서 돌아간다. Kubelet에 의해 실행되는 것으로 추측된다.

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

## 1.19 버전 설치

- VM에서의 실습과 동일하게 `/etc/hosts`에 private ip로 호스트네임을 등록하면 호스트네임으로 Ping을 보낼 수 있다.

- 1.19 버전은 도커를 컨테이너 런타임으로 한다. 그러므로 도커를 먼저 마스터 노드와 워커 노드에 설치한다.

```bash
curl https://download.docker.com/linux/centos/docker-ce.repo -o /etc/yum.repos.d/docker-ce.repo

```

```bash
cat <<EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF
```

- 설치된 도커에 추가적인 설정을 한다.

```bash
sudo cat <<'EOF' > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-$basearch
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
```

- 버전을 지정해서 설치해야 하기 때문에 repo 스크립트를 작성한다.

- 그다음 `yum -y install kubeadm-1.19.16-0 kubelet-1.19.16-0 kubectl-1.19.16-0 --disableexcludes=kubernetes`으로 kubeadm, kubelet, kubectl을 설치한다.

- kubelet을 실행시키고

```bash
--- Master ---
# kubeadm init --apiserver-advertise-address=10.30.0.4 --pod-network-cidr=10.30.1.0/24
# mkdir -p $HOME/.kube
# cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
# chown $(id -u):$(id -g) $HOME/.kube/config
# kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
```

- Worker Node에서

```bash
kubeadm join 10.30.0.4:6443 --token cfx87q.y2aj1m7w63jrue2v \
    --discovery-token-ca-cert-hash sha256:0f7de7b272558681b65cf906824b58e36d34c77808b839a8603f3251959e9212
```

- 클러스터 구축이 완료된다.
