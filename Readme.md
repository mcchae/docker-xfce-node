#  목적

지난번 [mcchae/docker-xfce](https://github.com/mcchae/docker-xfce) 에서 만든 것 처럼 alpine linux에 xfce 윈도우 환경을 갖춘 것에 다음과 같은 추가 작업을 진행해 보았습니다.

## 작업 내용

* nodejs : 버전 9.5.0 과 npm 5.6.0
* openjdk8 : alpine용 openjdk 버전 8을 설치하였습니다.
* WebStorm : 2017.3.4 버전의 pyCharm IDE를 설치하였습니다.


## 사용법

### docker 이용

```bash
# 내 데스크탑 이름
MYDT=rdp
# docker host에서 사용할 RDP 포트
HP_RDP=33899
# docker host에서 사용할 noVNC 포트
HP_NOVNC=60811
docker container run -it \
	--name $MYDT \
	--hostname $MYDT \
	-p $HP_RDP:3389 \
	-p $HP_NOVNC:6081 \
	-v /dhv/xfce/root:/root \
	mcchae/xfce-node
```

> `-it` 대신 `-d` 옵션을 이용하면 백그라운드 서비스로 실행됩니다.

### docker compose 이용

`~/mydt.yml` 이라는 이름으로 다음의 내용을 저장합니다.

``` yaml
version: '2'
services:
  mydt:
    image: "mcchae/xfce-node"
    hostname: "mydt"
    environment:
      VNC_GEOMETRY: "1440x900"
    ports:
     - "33899:3389"
     - "60811:6081"
    volumes:
     - ${HOME}/dhv/root:/root
     - ${HOME}/work:/root/work
```

> * `VNC_GEOMETRY` 환경변수는 해상도를 지정합니다. xrdp 또는 noVNC 모두 적용됩니다. (위의 예에서는 1440x900 해상도로 지정하였습니다)
> * `3389` 포트는 외부로 노출될 원격데스크탑 포트입니다. (위의 예에서는 33899 포트로 접근 가능합니다)
> * `6081` 포트는 외부로 노출될 noVNC 포트입니다. (위의 예에서는 60811 포트로 접근 가능합니다)
> * docker를 돌리는 호스트에 `$HOME/dhv`라는 폴더가 있고 이 폴더에 있는 `root` 디렉터리가 docker 컨테이너의 `/root` 디렉터리로 볼륨 마운트되어 root 홈 폴더는 영속성을 갖습니다
> * `$HOME/work` 라는 폴더에 모든 프로젝트가 있고 작업을 하는데 이것이 컨테이너의 `/root/work`로 볼륨 마운트되어 프로젝트 작업을 합니다
> * 윈도우인 경우에는 볼륨 마운트에서 `//c/Users/mcchae/dhv/root:/root` (사용자가 mcchae 라고 가정) 와 같은 방식으로 마운트 합니다. (윈도우 docker가 디폴트로 해당 사용자 부분을 마운트 가능하도록 해 놓았습니다)


그 다음, 다음과 같이 실행합니다.

```sh
$ docker-compose -f ~/mydt.yml up
```

> * `-d` 옵션을 이용하면 백그라운드 서비스로 실행됩니다.
> * `docker-compose -f ~/mydt.yml logs` 명령으로 컨테이너의 로그를 확인합니다. (특별히 -d 로 up 하였을 경우)
> * `docker-compose -f ~/mydt.yml down` 명령으로 컨테이너를 내립니다.


# 결론

FrontEnd용 IDE를 이용한 디버깅 환경의 컨테이너로 유용합니다

어느 분께는 도움이 되셨기를...

