# Pod

- Pod는 Node 내에 존재하는 쿠버네티스에서 생성하고 관리할 수 있는 배포 가능한 가장 작은 컴퓨팅 단위이다.

- Pod 내에는 복수의 컨테이너가 존재할 수 있으며 각 컨테이너는 하나의 호스트로 묶여있다고 볼 수 있으며 그렇기 때문에 컨테이너에는 하나의 포트만 할당된다.

```yaml
apiVersion: v1
metadata:
  name: pod-1
spec:
  containers:
    - name: container1
        image: tmkube/p8000
        ports:
        - containerPort: 8000
    - name: container2
        image: tmkube/p8000
        ports:
        - containerPort: 8080
```

- 위와 같이 yaml 파일을 통해 두개의 컨테이너를 가진 Pod를 생성할 수 있다.

## label

- 사용 목적에 따라 Pod에 Label을 붙인다.

- label은 key : value 형태로 이루어져 있다.

- 특정 Label의 Pod들을 Service에 매칭하여 원하는 종류의 Pod들에 연결된 Service를 생성할 수 있다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-2
  labels:
    type: web
    lo: dev
  spec:
    containers:
      - name: container
        image: tmkube/init
```

```yaml
apiVersion: v1
metadata:
  name: svc-1
spec:
  selector:
  type: web
  ports:
    - port: 8080
```

- 위와 같이 label을 명시한 Pod를 생성하고 selector를 사용해서 해당 label을 가진 Pod를 지정해서 Service를 생성할 수 있다.

## Node Schedule

- Pod가 속한 Node를 정하는 것이다.

- 사용자가 지정할 경우 Pod 생성 스크립트에 아래와 같이 생성할 Node의 hostname을 표기해서 생성할 수 있다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-3
spec:
  nodeSelector:
    hostname: node1
  containers:
    - name: container
      image: tmkube/init
```

- 사용자가 지정하지 않을 경우 각 노드가 가진 자원의 현황을 파악하고 수용 가능한 Node에 임의로 생성된다.

- 이 임의의 할당을 Node Scheduler가 수행한다.

- 스케줄러가 작업을 수행하기 위해서는 Pod가 사용하는 자원의 양을 명시해야 한다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-4
spec:
  containers:
    - name: container
      image: tmkube/init
      resources:
        requests:
          memory: 2Gi
        limits:
          memory: 3Gi
```

- requests에 최소 요구 자원양을 명시하고 limits에 요구 상한선을 명시한다.

- limits가 넘는 경우 메모리 사용량이 초과한 것이라면 Pod를 종료시키고 CPU 사용량이 초과했다면 requests 만큼 사용량을 낮추고 초과하더라도 종료시키지 않는다.
