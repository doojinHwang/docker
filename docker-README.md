# docker 내용정리

## docker 개요

#### image 와  container 의 큰 차이점
-image: disk에 존재  
-container : 메모리에 존재  

#### docker host
docker가 엔진이 설치된 머신
다수의 contaniner 존재(설치 가능)

#### namespace
독립환경을 만들고(격리) 이 환경에서 애플래케이션 실행  

#### cgroup
host의 리소스를 공유

(참고) 리눅스 커널의 고유 기능: namespace, cgroup


#### cnetOS ip확인 명령
	ip a s

#### docker version 확인
	docker version
1) EE(docker enterprise 버전) 
2) CE(open source): 실습시 사용  
 가. Edge - 1 월  주기로 개발됨, -최신버전이나 안정되질 않음    
 나. Stable - 3개월 주기로 update 기술지원은 4개월까지 <== 실습시 사용 버전  

 centOS7 docker설치: https://docs.docker.com/install/linux/docker-ce/centos/  

## 1. docker 사전 설치
1.yum 설치(사전 package설치]

	$ sudo yum install -y yum-utils \
	  device-mapper-persistent-data \
	  lvm2

	$ sudo yum-config-manager \
	    --add-repo \
	    https://download.docker.com/linux/centos/docker-ce.repo


## 2. docker 설치
	$ sudo yum install docker-ce docker-ce-cli containerd.io
	$ sudo systemctl start docker 
	$ sudo systemctl enable docker 

참고 : bash-completion 자동완성 패키지
	$ sudo yum install bash-completion
	=> yum list bash-completion


## 3. docker 명령어
(참고) sudo docker ~ 
==> root권한이면 sudo 생략가능하지만 사용자 계정이면 sudo를 사용

### 이미지 검색
> docker serach <이미지명>

    $ docker search cnetos
    
### docker이미지 다운로드 
> docker pull 이미지(허브ID(저장소ID)/이미지명:태그)
==> 저장소ID 생략시 기본 docker hub id

    $ docker pull httpd:latest
    
### docker 이미지 목록조회
	docker images
	docker image ls
    
### 컨테이너 생성
> docker create 이미지
    
### 컨테이너 시작
> docker start 이미지

> docker start -a(attach) 이미지명
==> shell 명령

> docker start -ai 이미지명 
==> 내부 shell 명령실행으로..
(참고)
내 bash 가 실행된 상태에서 빠져 나오기.
exit: 실행중인 컨테이너가 종료되면서 빠져나옴.
ctrl + p + q : 실행 컨테이너가 종료하질 않고 bash만 빠져나욤



### 컨테이너 중지
    docker stop 이미지 

### 컨테이너 실행
    docker run [옵션] 이미지명
==> run: create + start 2개 명령어 같이 실행한 것과 같음

    docker run -itd --name web httpd:latest
==> -it 옵션을 붙여야 shell 이미지 실행가능
==> --name 옵션은 컨테이너에 원하는 이름부여, 만약 생략시 docker에서 자동으로이름부여 함.

    docker run -d --name web httpd:latest
==> -d: background로 실행, -it: shell 명령실행 

(참고) -d옵션으로 실행후 다시 컨테이너 접근시: docker attach 이미지명 

    docker attach web

(서비스 컨테이너에 bash명령 삽입)

    docker run -it --name web httpd:latest bash
==> 해당 이미지에 shell이 있어함.
==> 컨테이너 실행시 bash로 접속되여 명령서 실행 가능


### docker image 삭제
> docker rmi <이미지명>
    
    docker rmi apache

(참고) hard link : 
- file -> I-node  : file에 대한 링크 정보를 같고~~~

### docker tag 부여
hard link를 붙인다는 의미 

> sudo docker tag 허브ID(onsoftel)/리포지토리이름:태그

    sudo docker tag centos:latest redface7/2019-02-cccr:centos
    sudo docker tag centos:latest onsoftel/2019-02-cccr:centos
    
### dokcer help 명령어
    sudo docker push --help 

### docker image push
> sudo docker push 허브ID/리포지토리이름:태그 

    sudo docker push dj/httpd:latest

### sudo docker ps
컨테이너 목록 조회

    docker ps
=> 실행중니 컨테이너만 조회

    docker ps -a
=> -a: 컨테이너 실행/중지 all전부 조회
    

### docker inspect
컨테이너(이미지) 세부정보 확인

    sudo docker inspect centos
==> "RoofFs": 영역이 layer 정보 임.
==> 이미지인지/컨테이너인지 구분은 상세정보 중에 network정보로 구분, 즉 IP가 부여된 것은 컨테이너


(참고) docker 이미지 종류
(한재경 강사 의견 기준)
- shell 이미지: 세부정보 내용중 Config정보에서 CMD 영역이: /bin/bash 있으면 해당
- service 이미지






### 쉡접속명령
    sudo docker attach <컨테이너명>
    
### doker volume ls

###  curl ip : 서비스 내용 확인(예: html소스보임)



### 컨테이너에서 실행되고 있는 프로세스 목록을 출력
> docker top <컨테이너 이름, ID> <ps 옵션>
host기준으로 보임
==> -ef 많이 사용
(참고) ps 옵션
-a: 모든 사용자의 프로세스를 출력합니다.
-u: 각 프로세스의 사용자(UID)를 표시합니다.
-s: 시그널을 표시합니다.
-v: 가상 메모리를 표시합니다.
-x: 자신을 실행시킨 셸이 없는 프로세스를 출력합니다.
-c: 커널에서 사용되는 프로세스 이름으로 표시합니다.
-e: 환경 변수를 표시합니다.
-f: 내용을 완전한 형태로 표시합니다.
-l: 자세한 형식으로 출력합니다.
-n: WCHAN 값을 숫자료 출력합니다.

### 실행중인 컨테이너 수정
 > sudo docker exec <컨테이너명>
 -i -t 옵션 많이 사용 
 
    $ sudo docker exec -it web bash
==> bash shell 포함 http같이 실행됨.
(참고) apache 이미지의 http.conf 파일 위치 확인: cd DocumnetRoot httd.conf
==> exec 실행된 컨테이너 에서 exit로 빠져나오면 컨테이는 종료되질 않고 bash(shell) 만 종료됨.


### 컨테이너 이름 변경
docker rename <기존이름> <새이름>


 ### 컨테이너 일시중지/해제
 > docker pause <컨테이너 이름>
 > docker unpause <컨테이너 이름>

 * 전체 컨테이너 정보확인: cpu, memory사용량 등
  docker stats --no-stream

### 컨테이너 삭제
> docker rm <컨테이너>

    docker rm httpd
==>  실행중인 컨테이너는 삭제 안됨.

### 실행중인 컨테이너 강제 삭제
> docker rm -f 이미지명

    docker rm -f httpd
    
### 모든 컨테이너 삭제
    docker rm -f $(sudo docker ps -aq) 
==> -q는 id만 보이기, docker ps -aq: 모든 컨테이너 id 조회
==> $( docker ps -aq) 리눅스 명령어으로 ()안에서 얻어 id에 대한 모든 컨테이너 대상
==> 이 명령은 신중하게 모든 컨테이너 삭제됨!!!







### docker host에 있는 file을 넣거나빼기 
    docker cp --help
(참고) 
-원격접속해서 파일복사: $ scp host:path
-작업디렉토리 확인하기: sudo docker exec web pwd
  
경로 확인후 copy
      sudo docker cp web:/usr/local/apache2/htdocs/index.html . <== .은 현재 디렉코리
      sudo vi index.html <== 수정후 
      sudo docker cp index.html web:/usr/local/apache2/htdocs
      sudo docker exec web cat /usr/local/apache2/htdocs/index.html

### 상태변경 확인
> sudo docker diff <이미지명>
 
    sudo docker diff web
==> layer 변경 상태 확인



### 이미지 만들기
실행중인 컨테이너로...

commit, export

    docker commit --help
(주의) [ REPOSTORY[:TAG] ] 룰을 지켜라, 옵션이지만 가능하면 사용하라..

    docker commit web dj/httpd:1


    sudo docker run -it --name c1 centos:latest
==>shell bash접속상태.


#### (1) httpd 설치
    yum install httpd

#### (2) httpd 실행
    systemctl start httpd 
==> httpd 실행, Failed 발생

#### (3) httpd enable 설정
    systemctl enable httpd
    
    # ls -l /etc/system/multi-user.target.wants/httpd.service

    # cd /usr/local/
    # vi /usr/lib/systemd/system/
    # vi /user/lib/systemd/system/h
    해당 httpd.service 문서 vi로 열어로 실행 명령어 복사 후 
    # /usr/sbin/httpd -DFOREGROUND & <== &백그라운드로 실행하라명령!!

### (4) command 명령으로 서비스 실행 
    $sudo run -d --name webtest dj/httpd:2 /usr/sbin/httpd -DFOREGOUND
==> inspect "Cmd" 영역이 bash 명령으로 지정됨.




<hr/>


## docker 저장소 : 폐쇄망 

### 모든 이미지를 archive으로 저장
> docker save --help

    docker save -o save.tar httpd alpine centos 
==> 이미지 3개(httpd, alpine centos)를 save.tar로 만듬.

    $ ls
    $ sudo file save.tar
    $ sudo tar tf save.tar <= 파일내용정보보기 

### archive(압축) 해제 
> docker load --help
    
    docker load -i save.tar


### 컨테이너 export
    docker run -d --name h1 httpd
    docker export --help
    docker export h1 -o export.tar
    sudo tar -tf export.tar | grep index.html <=압축 파일내 index.html 확인

### 컨테이너 import
    sudo docker import --help
    sudo docker import <파일>  [ REPOSITORY[:TAG] ]
    sudo dockr import export.tar dj/export:latest
==> layer가 통합됨으로 1개 존재하고 config설정이 안된 상태임(docker inspect로 확인 가능)
==> docker run -itd --name e1 dj/export:latest 실행 안됨, 설정안된 상태...
==> docker run -itd --name e1 dj/export:latest bash

    sudo docker commit e1 dj/export:commit <== docker run 시 명령동작 됨.




## docker 네트워크
(참고) https://docs.docker.com/network/ 

    $ sudo yum -y install bridge-utils #bridge-utils 설치
    $ brctl show
    $ sudo docker run -itd --name a1 alpine
    $ sudo docker run -itd --name a2 alpine
    $ sudo docker run -itd --name a3 alpine
여러개 생성후 brctl show 명령어 실행시  interfaces항목이 보임
==>bridge-utils는 NIC(Network Interface Card) 두개를 하나로 묶어 브릿지로 만들어 주는 역할  
정식 프로젝트 명칭은 IEEE 802.1d ethernet bridging으로 IEEE의 표준 이더넷 브릿지 구현.  
        
        
    $ sudo docker network ls
    $ sudo docker inspect bridge

    $ sudo docker network
-macvlan : 가상의 주소를 부여 


    $ sudo docker run -itd --name a2 --network host alpine
    $  sudo docker exec a2 ip a s


    $ sudo docker run -itd --name a3 --network none alpine <== none설정으로 외부에서 접속 안됨

    $ sudo docker network create --help


    $ sudo docker network create --subnet 192.168.0.0/24 net1
	cidi표기법:ip주소/subnet ~255를 > prefix로 벼환시 24개

	$ sudo docker network ls
	$ brctl show

	 sudo docker network ls

	$ sudo docker run -itd --name a2 --network net1 alpine
	sudo docker exec a2 ??
	sudo docker run -d --name h1 httpd
	sudo docker exec a1 ping 172.17.0.2

### network 연결
커스텀 네트워크 연결, 내부에 dns프로세스 동작됨(양방향 통신????)

	sudo docker network connect --help
	sudo docker network connect net1 h1 <= net1, h1 연결 후
	($sudo docker network connect net1 web)
	sudo docker inspect net1  
	==> insept 로 내용확인 하면 net1 ip, h1 네트워크 정보 확인과 연결 네트워크 정보 확인 후 해당연결 IP확인으로 통신 가능

	$ sudo docker exec a32 ping 192.168.0.3 <== a32에서 web(h1-192.168.0.3-) 으로 ping 실행으로 연결됨 확인...






## docker link
	$ sudo docker --link 
==> 단방향????

	$ sudo docker run -itd --name a1 alpine
	45c0857da0f64907e50b4af2054a146b083a31f0c3458b61e92a879ca8ccb3c4
	
	$ sudo docker run -itd --name a2  alpine
	3ba79bbbbadc97f622c14bf3572ae60354ac0e5fe552cb9634b65b9e3282af1a
	
	$ sudo docker rm -f a1
	a1
	
	$ sudo docker run -itd --link a2 --name a1 alpine
	0568afcd7ed85f0ec6cd32299168483b715dedfd7062612519456c117c82bf0c
	==> a1에서 a2로 연결 참고: a2가 db역할


	$ sudo docker exec a1 ping -c3 a2
	
	$ sudo docker exec a1 cat /etc/hosts
	
	$ sudo docker exec a2 ping -c3 a1


	sudo docker run -itd --link a2:db --name a1 alpine <= 별칭 지정: :db
	sudo docker exec a1 ping -c3 db

	$ sudo docker exec a3 cat /etc/hosts
	127.0.0.1	localhost
	::1	localhost ip6-localhost ip6-loopback
	fe00::0	ip6-localnet
	ff00::0	ip6-mcastprefix
	ff02::1	ip6-allnodes
	ff02::2	ip6-allrouters




## docker 포트 지정
	$ sudo docker run -d --name -p 8080:80 httpd


	hotsnamectl set -????


	$ ip a s eth0 <== 네트워크 확인


### 방화벽 열기: 
	sudo firewall-cmd -add (--reload) http
	systemctl status httpd
	
#### 네트워크 사용 port확인
	sudo ss -ntip





## docker volume

	$ sudo docker volume ls
	$ sudo docker volume create testvol
	$ sudo docker volume ls
	$ sudo docker inspect testvol <== volume위치 확인 


(참고) sudo -i : sudo 권한으로 접근(root)

	$ cd/var/lib/docker/volumes/testvol/_data
	
(참고) pwd : 현재 위치 확인 

	sudo docker run -d --name h1 -v testvol:/usr/local/apache2/htdocs -p 80:80 httpd



## disk 추가
	$ lsblk 
	==> disk 정보보기 


	$ sudo docker run -d -it --name c1 -v centvol:/tmp/dira centos
	$ df -h <==현재 사용량 정보보기 

	$ sudo docker run -itd --name c2 -v vol1:/tmp/vol1:ro centos <== :ro는 read only 옵션

	sudo docker exec c1 'mount' | grep vol1'
	sudo docker exec mount c1


	$ sudo docker volume prune <= mount삭제

db는 보통 자동 volume 설정 상태 
==> inpsect 내 정보 중 volumes 정보있음





## Container log 확인

	$ sudo docker logs -t d1 <-t time...
==>MYSQL_RANDOM_ROOT_PASSWORD: 사용 비번막기..  

mysql:5.7내 inspect설정 정보중 Entrypoint 설정 예: docker-entrypoint.sh를 실행 설정상태  


우선순위: Entrypoint > Cmd ???  

	sudo docker cp sharp



	$ sudo docker ps -a
	CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                      PORTS               NAMES
	0897e2840a43        mysql:5.7           "docker-entrypoint.s…"   24 minutes ago      Exited (1) 24 minutes ago                       d1
	
	$ sudo docker logs -t d1
	2019-07-09T15:37:48.524148988Z error: database is uninitialized and password option is not specified 
	2019-07-09T15:37:48.524224646Z   You need to specify one of MYSQL_ROOT_PASSWORD, MYSQL_ALLOW_EMPTY_PASSWORD and MYSQL_RANDOM_ROOT_PASSWORD

	$ sudo docker run -d --name d2 -e 'MYSQL_ROOT_PASSWORD=1234' mysql:5.7
	a9e6b3dab4b6209e70d52fa4dfd41f4ca905f3804979e9e8bc94ba1470b26d45
	
	$ sudo docker ps
	CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
	a9e6b3dab4b6        mysql:5.7           "docker-entrypoint.s…"   9 seconds ago       Up 7 seconds        3306/tcp, 33060/tcp   d2
	
	$ sudo docker top d2
	UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
	polkitd             214





## docker 자원제한

### container 메모리 제한
	$ sudo docker stats --no-stream <== 실행중인 컨테이너 정보한 보기
	$ free -h


	$ sudo docker run -d --name h1 --memory=512M httpd
	715a2c25b02405d128c0c62bce1b2bed98469017a31268c5bae433b2e5240c40
	
	$ sudo docker stats --no-stream
	CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT   MEM %               NET I/O             BLOCK I/O           PIDS
	715a2c25b024        h1                  0.02%               2.293MiB / 512MiB   0.45%               656B / 0B           0B / 0B             82
	[devops@docker ~]$ 

	
	$ sudo docker update h1 --memory=768M <== 메모리 수정 
	h1
	
	$ sudo docker stats --no-stream
	CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT   MEM %               NET I/O             BLOCK I/O           PIDS
	715a2c25b024        h1                  0.02%               2.293MiB / 768MiB   0.30%               656B / 0B           0B / 0B             82
	
	$ sudo docker update h1 --memory=1025M <== 초과지정시 에러발생 , 용량을 최대값을 늘릴려면 inspect내 memory-swap값을 수정
	Error response from daemon: Cannot update container 715a2c25b02405d128c0c62bce1b2bed98469017a31268c5bae433b2e5240c40: Memory limit should be smaller than already set memoryswap limit, update the memoryswap at the same time

	$ sudo docker update h1 --memory=1025M --memory-swap=1400M
	h1
	
	
	$ sudo docker stats --no-stream
	CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
	715a2c25b024        h1                  0.03%               2.293MiB / 1.001GiB   0.22%               656B / 0B           0B / 0B             82






### container cpu 제한
	docker run -itd --name a1 -c 10 이미지명 <== -c 10 cpu를 10% 할당


	$ sudo docker run -itd --name a1 -c 10 alpine
	843257bd67e67a1afc92ec67d1b4f141da9e0450dda338dcb4f82a0c1a66ef28
	
	$ sudo docker run -itd --name a2 -c 10 alpine
	d0b06c61a2b36a0daa56216c6b72d35c9755200602d72f470ac14105678d1fa3
	
	$ sudo docker run -itd --name a3 -c 20 alpine
	0411df22c2fa8f0d6d62f60ab73e038e18d7b896f7ad07c4fecaafb15619ad83
	
	$ sudo docker ps -a
	CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
	0411df22c2fa        alpine              "/bin/sh"           11 seconds ago      Up 9 seconds                            a3
	d0b06c61a2b3        alpine              "/bin/sh"           20 seconds ago      Up 19 seconds                           a2
	843257bd67e6        alpine              "/bin/sh"           32 seconds ago      Up 30 seconds                           a1
	
	$ sudo docker stats --no-stream
	CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT   MEM %               NET I/O             BLOCK I/O           PIDS
	0411df22c2fa        a3                  0.00%               128KiB / 1.952GiB   0.01%               656B / 0B           0B / 0B             1
	d0b06c61a2b3        a2                  0.00%               132KiB / 1.952GiB   0.01%               656B / 0B           0B / 0B             1
	843257bd67e6        a1                  0.00%               128KiB / 1.952GiB   0.01%               656B / 0B           0B / 0B             1


#### 임의로 CPU 사용량 증가 TEST
> $ sudo docker exec a1 dd if=/dev/zero of=/dev/null & 

	$ sudo docker exec a1 dd if=/dev/zero of=/dev/null &
	$ sudo docker exec a2 dd if=/dev/zero of=/dev/null &
	$ sudo docker exec a3 dd if=/dev/zero of=/dev/null &

#### 실시간 사용량 확인 
	$ sudo docker stats -a --all




### 쓰기 제한
참고: 교재: 68page





## Docker file
(작업 프로세스)
-mkdir centweb  : centweb 디렉토리만들기
-cd centweb/    : 디렉토리 접근
-vi Dockerfile  : Dockerfile 작성
-cat Dockerfile : Dockerfile 내용 확인

### Dockerfile 작성 규칙
	FROM centos:latest <==base이미지지정, 2개이상도 지정가능하나, 기본 1개만 지정
	MAINTAINER	<== 작성자 지정
	RUN yum -y install httpd <==RUN: 명령어 지정, 형식 2가지(shell, exec)있으나 어떤가 사용ㅎ도 무방
	CMD /usr/sbin/httpd -DFOREGROND <==CMD: 데몬 실행, 한 개의 지시어만 사용 가능 (형식: run과 같은 shell, exec 2가지)
	LABEL : 라벨설정
	EXPOSE	: 포트 내보기
	ENV <= 환경변수 설정, Key, value를 지정(2가지 형식: 1) 공백으로 구분 KEY VALUE, 형식 2) =으로 구분 KEY=VALUE 단, value값에 공백이 있으면 앞에 '\' 붙여야 함.
	ADD <== 파일 추가
	COPY <== 파일복사
	VOLUME <== 볼륨 마운트
	ENTRYPOINT <== 데몬 실행, CMD 실행전에 사전 실행지정(예: web서버전에 db실행 지정 등...)
	USER <= 사용자 설정(USER NAME(or UID)), 
		(특정 사용자가 실행가능하도록 저정 예) 
				RUN user add test 
				USER test
	WORKDIR <== 작업 디렉토리 지정(RUN, CMD, ENTRYPOINT, COPY, ADD 명령을 실행을 위한 지정)
	ONBUILD <== build 후 실행 명령, 베이스 이미지를 기반으로 다른 이미지를 생성할대 실행 되도록 지정 (예: ONBUILD RUN touch /tmp/filea,  ONBUILD ADD index.html /var/www/html)


	$ docker build -t base:1.0(이미지명) /root/file/ 
	==> /root/file : Dockerfile존재경로 



	cat devops
	devops ALL=(ALL) NOPASSWD: ALL
	pwd
	/etc/sudoers.d


*Traditionally, the Dockerfile is called Dockerfile and located in the root of the context. You use the -f flag with docker build to point to a Dockerfile anywhere in your file system.  

	==> $ docker build -f /path/to/a/Dockerfile .

*You can specify a repository and tag at which to save the new image if the build succeeds:

	==> $ docker build -t shykes/myapp .


	sudo docker run -it --name c1 dockerfile:centweb







## 저장소 만들기

	$ docker run -d -p 5000:5000 --restart always --name registry registry:2 
	==> --restart always: 도커 재시작 해도 실행 되도록..

	$ docker run -d -p 5000:5000 --restart always --name -v regvol:/var/lib/registry  registry registry:2 
	==> -v regvol:/var/lib/registry : 저장위치 지정 

	$ sudo docker tag test:shell localhost:5000/centos/web  
	==> test:shell($ sudo docker images결과에서 나타나는 REPOSITORY:TAG)  
	==> localhost:5000/리포지토리이름/이미지명:태그  
	$ sudo docker push localhost:5000/centos:web



	sudo docker tag alpine docker.nobreak.co.kr:5000/alpine:cccr
	==> 에러발생함, 인증서 필요.

인증서 파일 만들기.(docker insecure 인시큐어 ) <= https://docs.docker.com/registry/insecure/  
파일작성시 DNS 수정이 필요하나 불가로 host파일 수정(vi /etc/hosts) <= 수정 후 systemctl restaret docker로 재시작.  





## 멀티 컨테이너 관리: Docker Dompose
docker는 기본 단일 host로 관리 됨~ㅠㅠ  

### Dockr Compose
다수의 컨테이너를 하나로 통합하여 관리할 수 있는 도구  


#### 설치
	sudo curl -L "https://github.com/docker/compose/releases/download/1.24.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
	
#### 권한설정:
	sudo chmod +x /usr/local/bin/docker-compose
	
#### 버전확인 : 
	docker-compose --version


(참고)
compose-file 의 services는 컨테이너 이름과 틀리다!!!!, services는 다수 존재가능하며, 별도 이름지정할 필요는 없음.  




## 멀티 호스트 관리: docker swarm

https://docs.docker.com/engine/reference/commandline/swarm_init/



(dockerDir)폴더 모두 삭제

	$ rm -rf dockerDir  <= 리눅스 명령



(참고): ls _manifests/ 
https://docs.docker.com/engine/reference/commandline/manifest_inspect/






<hr/>

[참고]

	sudo ps -ef | grep bash
	ps $$
	sudo kill -9 3030

	cat > index.html : index.html 바로수정 됨(입력글자) <= 비권장 

웹서버 용량비교: httpd:alpine(5.5MB), centos(202MB)

<hr/>


[참고]
Docker.ova centOS 계정: devops / qwer1@34

