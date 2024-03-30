# ALB-API
> ALB를 통한 API 통신 설정, HTTPS 보안 통신 설정

<br>

## 들어가기 전 (AWS 사용자단 App 구축)
### ReactNative - Expo App (Frontend)
> 설치 프로세스
1. git clone
2. sudo apt-get update -> sudo apt-get upgrade
3. sudo apt install nodejs 대신에, nvm으로 특정 버전 바로 설치

<br>

- NVM으로 Node 설치 진행 순서
```
sudo apt-get install curl gnupg2 -y

curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
-> Close and reopen your terminal to start using nvm

source ~/.bashrc

nvm --version
```

<br>

4. nvm install 18.19.0 -> nvm ls -> nvm use 18.19.0
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

<img src="https://github.com/kksung/ALB-API/assets/110016279/dfb77095-5f62-4b01-b600-ca1a96baff3c" width=830 height=300>

- 보안그룹 로드밸런서 리스너 포트 Open

<br>

### App Backend
<img src="https://github.com/kksung/ALB-API/assets/110016279/2ec958a7-1012-4e0b-960a-9fd9bfe70b42" width=200 height=450>

- Backend-API Server 파일 구조

<br>

<img src="https://github.com/kksung/ALB-API/assets/110016279/da3dfdd4-1267-4957-adbc-de6c298024ef" width=900 height=400>

- App 서버단에서는 inbound 규칙을 로드밸런서 보안그룹으로 설정

<br>

<img src="https://github.com/kksung/ALB-API/assets/110016279/0114d747-93ca-48bc-9283-cecc86b4a736" width=730 height=280>

- DB연결 코드 수정!

<br>

<img src="https://github.com/kksung/ALB-API/assets/110016279/fcc1c5d7-2129-4f23-bc87-cfe5cef7beda" width=800 height=330>

- 로드밸런서를 통한 통신 (로드밸런서 생성)

<br>

<img src="https://github.com/kksung/ALB-API/assets/110016279/cac9062b-18ac-4fbd-a4a8-951ff2cfc801" width=950 height=180>
<img src="https://github.com/kksung/ALB-API/assets/110016279/c760922b-ceb6-40db-93ab-0094ac6074ea" width=850 height=200>

- 로드밸런서 헬스체크 -> 대상그룹 Healthy 상태 설정
  - `curl -l http://1st-API-ALB-806911070.ap-northeast-2.elb.amazonaws.com:5000/`

<br>

<img src="https://github.com/kksung/ALB-API/assets/110016279/e866e65b-e6d2-478a-b3ab-5b0123e8d93b" width=800 height=400>

- talend를 통한 통신 확인 -> Login - POST

<br>

## ALB HTTPS 보안 통신 설정
<img src="https://github.com/kksung/ALB-API/assets/110016279/f5d87034-7779-483e-853a-33e0258877c6" width=850 height=90>

- 가비아에서 도메인 구매 (ssgfinal1jo.shop)

<br>

<img src="https://github.com/kksung/ALB-API/assets/110016279/a5f942ca-a4d3-4867-b682-d5d190b7614c" width=750 height=400>

- Route53 도메인 인증 (AWS단에서 도메인 소유 인증) -> 호스팅 영역 생성

<br>

<img src="https://github.com/kksung/ALB-API/assets/110016279/f7df3748-e701-491d-8231-544ed5a60d6e" width=850 height=250>

- NS 유형의 라우팅 대상 4개 -> 가비아 들어가서 입력

<br>

<img src="https://github.com/kksung/ALB-API/assets/110016279/9420f098-81a0-4023-ae1d-660667538153" width=850 height=300>

- 서브도메인까지 포함되는 인증서 발급 (* -> 애스터리스크로 발급)

<br>

<img src="https://github.com/kksung/ALB-API/assets/110016279/ee766b0a-e5be-4942-a428-bbfe8f70d898" width=750 height=400>

- 서브도메인 A레코드 생성 -> 각 LB별로 지정

<br>

<img src="https://github.com/kksung/ALB-API/assets/110016279/1a152bcc-5162-44b2-91d0-750ed48e8a14" width=900 height=530>

- 예시, 서브도메인별로 A레코드 생성 -> LB별 지정 확인

<br>

<img src="https://github.com/kksung/ALB-API/assets/110016279/a46b4158-b4e7-4446-a77c-4e387f0a9f3b" width=680 height=430>
<img src="https://github.com/kksung/ALB-API/assets/110016279/16b8164f-916f-4e08-b643-49c6596b4e4e" width=680 height=430>

- 로드밸런서 HTTPS:443 리스너 설정시 인증서 *.ssgfinal1jo.shop 선택


<br>

<img src="https://github.com/kksung/ALB-API/assets/110016279/4c9e8df9-fc5a-4894-946b-0a735c0a8c1c" width=900 height=140>

- 이제 `https://subdomain.ssgfinal1jo.shop:443`으로 통신 가능
- 서브도메인명으로 로드밸런서 443 리스너 라우팅 가능 -> '보안통신'

<br>

<img src="https://github.com/kksung/ALB-API/assets/110016279/131f7428-d0d3-4ce3-bbb6-963c06ef30ef" width=900 height=380>

- Talend를 통해 `https://appserver.ssgfinal1jo.shop/login` 으로 로그인 API 테스트 성공

<br>

## Troubleshooting & 유의사항
### 1 - ReactNative 접근 로드밸런서 설정 
- AWS단에서 exp://~:8081로 실행되어 접근해야하는 문제
- Expo URL 리다이렉트를 AWS 단에서는 지원하지 않아 구현 X

<br>

### 2 - 로드밸런서 생성 
- App서버단(private subnet에 위치) -> 로드밸런서가 내부로 트래픽을 전달하더라도, 내부가 아닌 외부로 생성해야함 (Internetfacing)

<br>

### 3 - 보안그룹 설정
- 로드밸런서의 보안그룹 설정 -> 인바운드 규칙에 리스너 포트 Open
- 로드밸런서 대상그룹 (모바일 앱 백엔드 서버) 보안그룹 설정 -> 인바운드 규칙에 '로드밸런서의 보안그룹'을 지정할 것
