#### Deploy 시작히기
- django 기본 프로젝트 만들기
- Hello world! 보여주기 화면 구성
- settings 파일의 주요 경로 수정
- runserver하여 화면 정상적으로 표시 확인

###### EC2 instance 생성
- EC2 : Elastic Compute Cloud
- aws에서 ec2접속, 우측 상단 region seoul확인
- Network & Security - key pairs
- create key pair(임의의 이름 지정)
- 다운받은 .pem파일을 ~/.ssh폴더에 넣기
- .ssh폴더가 없으면 폴더를 생성하고 pem파일 넣기
- pem 파일을 소유자만 읽을 수 있게 권한 변경
chmod 400 name.pem
---
- instane 생성. Launch instance 클릭
- ubuntu 16.04 - free tier 선택
- review and Launch
- edit security group
- soruce를 My ip로 변경
- Launch - keypair 선택
---
- 접속하기 :
ssh -i ~/.ssh/name.pem ubuntu@public DNS from aws
- locale 설정 :
sudo locale-gen ko_KR.UTF-8
---

### Ubuntu 기본 설정

#### python-pip설치

```
sudo apt-get update
sudo apt-get install python-pip
```

#### zsh 설치

```
sudo apt-get install zsh
```


#### oh-my-zsh 설치

```
sudo curl -L http://install.ohmyz.sh | sh
```


#### Default shell 변경

```
sudo chsh ubuntu -s /usr/bin/zsh
```

#### pyenv requirements설치

[공식문서]
https://github.com/yyuu/pyenv/wiki/Common-build-problems
```
sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
xz-utils tk-dev
```

#### pyenv 설치

```
curl -L https://raw.githubusercontent.com/yyuu/pyenv-installer/master/bin/pyenv-installer | bash
```

#### pyenv 설정 .zshrc에 기록

```
vi ~/.zshrc
export PATH="/home/ubuntu/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```


#### Pillow 라이브러리 설치

[공식문서](https://pillow.readthedocs.io/en/3.4.x/installation.html#basic-installation)

```
sudo apt-get install libtiff5-dev libjpeg8-dev zlib1g-dev \
    libfreetype6-dev liblcms2-dev libwebp-dev tcl8.6-dev tk8.6-dev python-tk
```



## Django 관련 설정

#### 장고 애플리케이션은 /srv Directory 사용

```
sudo chown -R ubuntu:ubuntu /srv/
```

[관련설명]  
<http://www.thegeekstuff.com/2010/09/linux-file-system-structure/?utm_source=tuicool>

#### 프로젝트 Clone or local에서 scp

```
git clone <자신의 프로젝트>
```
- ~/ .. .. 으로 루트폴더로 이동
- ubuntu에 권한 주기 : / chown -R ubuntu:ubuntu srv
- srv/에 app폴더 생성
- 로컬의 프로젝트 폴더 위치에서 scp명령으로 파일 보내기 :
scp -r -i ~/.ssh/name.pem . ubuntu@ec2-52-79-191-*****-northeast-2.compute.amazonaws.com:/srv/app/
- 서버의 srv/ pyenv install 3.5.2
- srv/app pyenv virtualenv 3.5.2 name
가상 환경 적용 뒤에서 서버밖으로 나갔다 와야함!
- sudo apt-get update 먼저 실행
- app(가상환경이 적용된)/ pip install -r requirements.txt
```
오류내용
"python setup.py egg_info" failed with error code 1 in /tmp/pip-build-7SrToZ/gitsome
sudo pip3 install git+https://github.com/donnemartin/gitsome.git
or
sudo -H pip3 install gitsome
sudo apt-get install python3-pip
sudo pip3 install --upgrade pip

```
- 위 과정에서 오류나면 서버밖으로 나갔다 재접속해서 install 다시 실행
---
- aws security group - 해당 instance :
inbound add rule로 추가(custom tcp rule / tcp / 8000 / anywhere)
- python manage.py runserver 0.0.0.0:8000
- ec2-52-79-******.ap-northeast-2.compute.amazonaws.com: 8000으로 접속하여 Hello world! 출력을 확인
- sudo apt-get dist-upgrade(설치된 유틸과 관련된 모듈도 같이 업그레이드해줌)
---
- ec2 재시작 : sudo shutdown -r now

##### uWSGI
application container server(application server)의 일종이다. application으로 향하는 길목의 (WSGI 형식의) interface 역할을 맡는다. 클라이언트의 HTTP 요청을 (우리의 경우 Python Web Framework인 Django로 application을 개발했으므로) application이 처리할 수 있는 Python 호출로 번역하는 역할을 맡는다.
여기서 웹서버는 nginx, 웹애플리케이션은 django를 뜻한다.


### uWSGI 관련 설정

#### 웹 서버 관리용 유저 생성

```
sudo adduser nginx
```

#### uWSGI설치

```
(virtualenv 환경 내부에서)

app/ pip install uwsgi
```

#### uWSGI 정상 동작 확인

```
uwsgi --http :8080 --home (virtualenv경로) --chdir (django프로젝트 경로) -w (프로젝트명).wsgi
```

ex) pyenv virtualenv이름이 mysite-env, django프로젝트가 /srv/mysite/django_app, 프로젝트명이 mysite일 경우

```
uwsgi --http :8000 --home ~/.pyenv/versions/deploy_practice_ec2 --chdir /srv/app/django_app -w deploy_practice_ec2.wsgi
```

실행 후 <ec2도메인>:8000으로 접속하여 요청을 잘 받는지 확인

#### uWSGI 사이트 파일 작성

```
sudo mkdir /etc/uwsgi
sudo mkdir /etc/uwsgi/sites
sudo vi /etc/uwsgi/sites/mysite.ini

[uwsgi]
chdir = /srv/mysite/django_app # Django application folder
module = mysite.wsgi:application # Django project name.wsgi
home = /home/ubuntu/.pyenv/versions/mysite # VirtualEnv location

uid = nginx
gid = nginx

socket = /tmp/mysite.sock
chmod-socket = 666
chown-socket = nginx:nginx

enable-threads = true
master = true
pidfile = /tmp/mysite.pid
```

#### uWSGI site파일로 정상 동작 확인

```
uwsgi --http :8080 -i /etc/uwsgi/sites/mysite.ini
```

sudo로 root권한으로 실행

```
sudo /home/ubuntu/.pyenv/versions/deploy_practice_ec2/bin/uwsgi --http :8000 -i /etc/uwsgi/sites/app.ini
sudo /home/ubuntu/.pyenv/versions/team-project_env/bin/uwsgi --http :8000 -i /etc/uwsgi/sites/app.ini
sudo /home/ubuntu/.pyenv/versions/team-project_env/bin/uwsgi --http :8000 -i /etc/uwsgi/sites/app.ini

```

#### uWSGI 서비스 설정파일 작성
껐다 켰을때도 작동할 수 있게
```
sudo vi /etc/systemd/system/uwsgi.service

[Unit]
Description=uWSGI Emperor service
After=syslog.target

[Service]
ExecPre=/bin/sh -c 'mkdir -p /run/uwsgi; chown nginx:nginx /run/uwsgi'
ExecStart=/home/ubuntu/.pyenv/versions/mysite/bin/uwsgi --uid nginx --gid nginx --master --emperor /etc/uwsgi/sites

Restart=always
KillSignal=SIGQUIT
Type=notify
StandardError=syslog
NotifyAccess=all

[Install]
WantedBy=multi-user.target
```

- sudo systemctl restart uwsgi
- ps -ax | grep uwsgi
---
- 수업에서는 nginx를 www-data로 변경함
-sudo systemctl daemon-reload
- sudo systemctl restart uwsgi
---

##### IAM(보안쪽 내용임. 참고만)
- console aws에서 iam 접속
- users - add user
- programmatic access 선택, next
- direct 선택, ec2검색하고 권한부여 범위 선택
- create
- key id와 secret key를 확인한다.
- 로컬의 프로젝트 폴더에서 pip install awscli
- aws configure
- id와 key 입력
- region: ap-northeast-2
- format: json
- cd ~/.aws
- vi config 의 내용 확인



## Nginx 관련 설정

#### Nginx 안정화 최신버전 사전세팅 및 설치

```
sudo apt-get install software-properties-common python-software-properties
sudo add-apt-repository ppa:nginx/stable
sudo apt-get update
sudo apt-get install nginx
nginx -v
```

#### Nginx 동작 User 변경

```
sudo vi /etc/nginx/nginx.conf

user nginx;
```

#### Nginx 가상서버 설정 파일 작성

```
sudo vi /etc/nginx/sites-available/mysite

server {
    listen 80;
    server_name localhost;
    charset utf-8;
    client_max_body_size 128M;


    location / {
        uwsgi_pass    unix:///tmp/mysite.sock;
        include       uwsgi_params;
    }
}
```

#### 설정파일 심볼릭 링크 생성

```
sudo ln -s /etc/nginx/sites-available/mysite /etc/nginx/sites-enabled/mysite
```

#### sites-enabled의 default파일 삭제

```
sudo rm /etc/nginx/sites-enabled/default
```

> nginx.conf파일에 어떤 폴더에 있는 설정을 가져와서 실행할 지 적혀있음


#### uWSGI, Nginx재시작

```
sudo systemctl restart uwsgi nginx
```

-

### 오류 발생 시

#### systemctl restart시 오류 발생 시

```
(오류 발생한 서비스에 따라 아래 명령어 실행)
sudo systemctl status uwsgi.service
sudo systemctl status nginx.service
```

#### 502 Bad Gateway

**Nginx log파일 확인**  
```
cat /var/log/nginx/error.log
```

#### Nginx log파일에서 sock파일 접근 불가시

**socket파일 권한 소유자 확인**

```
cd /tmp
ls -al
-rw-r--r--  1 nginx  nginx     6 Nov  8 06:58 mysite.pid
srw-rw-rw-  1 nginx  nginx     0 Nov  8 06:58 mysite.sock

nginx가 소유자가 아닐 경우,
sudo rm mysite.pid mysite.sock으로 삭제 후 서비스 재시작
```

## 서버 설정

### Media

> 본인의 media폴더 위치를 확인

`sudo vi /etc/nginx/sites-available/app`

```
location /media/ {
	alias /srv/app/media/;
}
```

**이미지 업로드시 Permission Denied발생 할 경우**  
`sudo chmod 777 media`로 `media`폴더의 권한을 변경, `media`폴더 하위 폴더 모두 삭제 후 다시 실행


### Static

> 본인의 static_root폴더 위치를 확인

`sudo vi /etc/nginx/sites-available/app`

```
location /static/ {
	alias /srv/app/static_root/;
}
```

### 데이터베이스
sudo apt-get install postgresql postgresql-contrib


> macOS에서는 `sudo -u postgres` 입력 불필요

**유저생성**  
`sudo -u postgres createuser -s -P <username>`

**유저삭제**  
`sudo -u postgres dropuser <username>`

**db삭제**  
`sudo -u postgres dropdb <db name>`

**db생성**  
`sudo -u postgres createdb <db name> owner=<owner name>`

-

#### AWS Secutiriy Groups 80 Port추가

Security Groups -> Inbound -> Edit -> HTTP


## Cloudflare

#### ALLOWED_HOSTS 추가



## SSL

> [Nginx에 SSL적용](https://haandol.wordpress.com/2014/03/12/nginx-ssl-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0startssl-com%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC/)  
> [Cloudflare에 Custom SSL적용](https://support.cloudflare.com/hc/en-us/articles/200170466-How-do-I-upload-a-custom-SSL-certificate-Business-or-Enterprise-only-)


## S3 연결

### boto3를 이용해서 S3 Bucket생성

`boto3`

```python
pip install boto3
```

```
>>> import boto3
>>> session = boto3.Session(profile_name='fc-4th')
>>> client = session.client('s3')
>>> client.create_bucket(Bucket='fc-4th-test', CreateBucketConfiguration={'LocationConstraint': 'ap-northeast-2'})
{'Location': 'http://fc-4th-test.s3.amazonaws.com/', 'ResponseMetadata': {'RequestId': '0B679A5EBF1FCF19', 'HostId': 'qLvVYj0n74HZKnA46m+ILCabPs71dt0GEqNFllrRguaBSbvQ2MpQ4aWhOT/PjHFzVo8nE1/H4cw=', 'HTTPStatusCode': 200, 'RetryAttempts': 0, 'HTTPHeaders': {'content-length': '0', 'location': 'http://fc-4th-test.s3.amazonaws.com/', 'date': 'Thu, 09 Mar 2017 01:41:17 GMT', 'x-amz-id-2': 'qLvVYj0n74HZKnA46m+ILCabPs71dt0GEqNFllrRguaBSbvQ2MpQ4aWhOT/PjHFzVo8nE1/H4cw=', 'x-amz-request-id': '0B679A5EBF1FCF19', 'server': 'AmazonS3'}}}
>>>
```


























###### Nginx
web server의 일종이다. 클라이언트와 uWSGI 사이에 위치하여 클라이언트의 요청을 uWSGI에게 Reverse Proxy 해주는 역할을 맡는다. 앞단에 들어오는 여러 다발(클라이언트는 여러명)의 요청을 한다발(Nginx와 uWSGI를 사이의 연결은 하나)로 묶어서 뒷단에 전달해준다고 생각해볼 수 있겠다. 또한 정적 컨텐츠를 처리할때는 Nginx의 performance가 더 뛰어나고, security feature 면에서도 뛰어나기 때문에 Nginx가 잘하는 부분은 Nginx에게 맡기는 것이다.
