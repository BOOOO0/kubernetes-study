# 가상화 기술의 역사

- 리눅스 시스템의 등장과 발전 과정에서 자원을 격리해서 프로세스들이 독립적으로 실행될 수 있는 자원 격리 기술이 등장했습니다. 그렇지만 이 기술은 사용이 어려워서 대중적으로 사용되지는 않았습니다.

- 그 후 VM 가상화 기술이 등장하고 대중적으로 이용되었습니다.

- 하지만 VM 환경에서의 자동화는 가상화를 위해 가벼운 서비스 하나를 가상화하기 위해서도 그것보다 무거운 OS를 포함시켜야 하는 상황이 발생해서 시스템 효율이 떨어지는 단점이 있었습니다.

- 2014년 dotCloud(Docker)라는 회사에서 자원 격리 기술을 사용해 컨테이너 가상화 기술을 개발했고 컨테이너는 게스트 OS를 포함하지 않고 가상화를 할 수 있는 장점이 있었습니다.

- 그 후 하나의 서비스가 아닌 여러 서비스를 배포 및 운영할 수 있는 컨테이너 오케스트레이터라는 개념이 등장했고 많은 기업들이 오케스트레이터를 출시했습니다.

- 컨테이너 오케스트레이터 중 하나인 쿠버네티스는 가장 많이 사용되며 표준이 되었고 AWS나 Azure같은 클라우드 서비스들은 쿠버네티스 환경이 설치된 인프라를 제공하고 있습니다.
