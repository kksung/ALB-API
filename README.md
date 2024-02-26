# ALB-API
> ALB를 통한 API 통신 설정, HTTPS 보안 통신 설정

<br>

## 들어가기 전 (AWS 사용자단 App 구축)
### ReactNative - Expo App (Frontend)
> 설치 프로세스
1. git clone
2. sudo apt-get update → sudo apt-get upgrade
3. sudo apt install nodejs 대신에, nvm으로 특정 버전 바로 설치
   - NVM으로 Node 설치 진행 순서
```
sudo apt-get install curl gnupg2 -y

curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
=> Close and reopen your terminal to start using nvm

source ~/.bashrc

nvm --version
```
4. nvm install 18.19.0 → nvm ls → nvm use 18.19.0
5. cd 폴더 -> npm install (package.json에 기재되어있는 모듈 모두 설치)
6. npx expo start
!expo://ec2publicip:8081로 접근해야하고 인바운드 규칙에 포트를 열어줘야 접근 가능

<br>

### Flask (Backend)
> Backend 패키지 설치 순서

1. sudo apt-get update -> sudo apt-get install python3 python3-pip

2. pip3 install -r requirements.txt

3. 보안그룹에 Appserver 포트 5000번 인바운드추가

5. python app.py 실행(코드변경은 추후 연동 및 통신시 환경변수 수정)

<br>

## 사용자단 App LB 통신을 위한 설정
> RN-FE, Flask-BE

<br>

### App Frontend
<img src="https://github.com/kksung/ALB-API/assets/110016279/90ae6fd1-1b79-4f09-a783-32b4bee311cd" width=200 height=400>

- RN구조, .env파일 -> API 쏘는 경로(로드밸런서로) 명시

<br>

<img src="https://github.com/kksung/ALB-API/assets/110016279/9d66a93d-631e-4362-99c3-ed373be40a7d" width=700 height=550>

- 모바일 Expo 앱 내에서 expo://인스턴스IP:8081로 접근해야함

<br>

<img src="https://github.com/kksung/ALB-API/assets/110016279/0e983912-93d8-4ea9-84f7-fc64e1ddc36b" width=750 height=400>

- 로드밸런서 외부(인터넷 경계) 생성

<br>

<img src="https://github.com/kksung/ALB-API/assets/110016279/dfb77095-5f62-4b01-b600-ca1a96baff3c" width=780 height=330>

- 보안그룹 로드밸런서 리스너 포트 Open

<br>

### App Backend
<img src="https://github.com/kksung/ALB-API/assets/110016279/2ec958a7-1012-4e0b-960a-9fd9bfe70b42" width=200 height=450>

- Backend-API Server 파일 구조

<br>

<img src="https://github.com/kksung/ALB-API/assets/110016279/da3dfdd4-1267-4957-adbc-de6c298024ef" width=900 height=430>

- App 서버단에서는 inbound 규칙을 로드밸런서 보안그룹으로 설정

<br>

<img src="https://github.com/kksung/ALB-API/assets/110016279/0114d747-93ca-48bc-9283-cecc86b4a736" width=730 height=280>

- DB연결 코드 수정!

<br>

<img src="https://github.com/kksung/ALB-API/assets/110016279/fcc1c5d7-2129-4f23-bc87-cfe5cef7beda" width=800 height=330>

- 로드밸런서를 통한 통신 (로드밸런서 생성)

<br>

<img src="https://github.com/kksung/ALB-API/assets/110016279/cac9062b-18ac-4fbd-a4a8-951ff2cfc801" width=975 height=200>
<img src="https://github.com/kksung/ALB-API/assets/110016279/c760922b-ceb6-40db-93ab-0094ac6074ea" width=850 height=200>

- 로드밸런서 헬스체크 -> 대상그룹 Healthy 상태 설정
  - 'curl -l http://1st-API-ALB-806911070.ap-northeast-2.elb.amazonaws.com:5000/'

<br>

<img src="https://github.com/kksung/ALB-API/assets/110016279/e866e65b-e6d2-478a-b3ab-5b0123e8d93b" width=800 height=400>

- talend를 통한 통신 확인 -> Login - POST

<br>

## Troubleshooting & 유의사항
### 1 - ReactNative 접근 로드밸런서 설정 
- AWS단에서 exp://~:8081로 실행되어 접근해야하는 문제
- url 리다이렉트가 aws단에서는 되지 않아 구현 x

<br>

### 2 - 로드밸런서 생성 
- App서버단(private subnet에 위치) -> 로드밸런서가 내부로 트래픽을 전달하더라도 내부가 아닌 외부로 생성해야함 (Internetfacing)
