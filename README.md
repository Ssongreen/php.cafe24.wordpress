# 🌐 동아전기 웹사이트 관리 및 문제 해결 여정 🛠️

본 문서는 Cafe24 호스팅 환경에서 운영 중인 동아전기 워드프레스 웹사이트([www.dongaelec.com](http://www.dongaelec.com))의 주요 관리 작업 및 발생했던 문제들에 대한 해결 과정을 기록한 트러블슈팅 가이드입니다. 워드프레스 사이트 운영 중 비슷한 문제에 직면했을 때 참고 자료로 활용될 수 있습니다.

## 목차

1.  [🚀 HTTPS 프로토콜 전환](#1-https-프로토콜-전환)
2.  [🔧 FTP 및 데이터베이스 접속 (MySQL Workbench)](#2-ftp-및-데이터베이스-접속-mysql-workbench)
3.  [🔑 워드프레스 관리자 정보 관리](#3-워드프레스-관리자-정보-관리)
4.  [🎨 워드프레스 콘텐츠 편집 (TheGem 테마, WPBakery, SA Sliders)](#4-워드프레스-콘텐츠-편집-thegem-테마-wpbakery-sa-sliders)
5.  [🗺️ HTML 레이아웃 구성 (공장 정보 페이지)](#5-html-레이아웃-구성-공장-정보-페이지)
6.  [🖼️ 관련 스크린샷 저장 위치](#6-관련-스크린샷-저장-위치)

---

## 1. HTTPS 프로토콜 전환 🛡️

웹사이트 보안 강화를 위해 HTTP에서 HTTPS로 전환하는 과정에서 발생한 이슈와 해결 과정을 탐색합니다.

* **초기 목표:** `http://www.dongaelec.com`을 `https://www.dongaelec.com`으로 변경.
   ![image](https://github.com/user-attachments/assets/e0a007b4-7e44-4a3e-87e8-27a161bea1ce)

* **주요 점검 사항:**
    * **SSL 인증서 상태 확인:** Cafe24 호스팅 패널에서 SSL 인증서가 정상적으로 발급 및 설치되었는지 확인하는 방법 탐색.
        * *발견된 문제:* 인증서 미설치 또는 오류 (`NET::ERR_CERT_AUTHORITY_INVALID`).
        * *참고 스크린샷 (SSL 오류): ![image](https://github.com/user-attachments/assets/90d5a79c-b960-4728-9b4f-8b1116ea7759)

        * *참고 스크린샷 (Cafe24 인증서 "미설치" 상태):* ![image](https://github.com/user-attachments/assets/6531095f-3185-49bd-a473-fa4d3c19510a) ![image](https://github.com/user-attachments/assets/68a24fa8-e667-4db1-a3d1-6ec224e78f6e)


        * *점검 과정:* Cafe24 내 SSL 인증서 HTTP 인증 절차 진행.
        
    * **리디렉션 루프 (`ERR_TOO_MANY_REDIRECTS`):** HTTPS 설정 중 무한 리디렉션 문제 발생.
  
        * *점검 포인트:*
            * `wp-config.php` 파일 내 `WP_HOME`, `WP_SITEURL` 설정값 확인 및 수정.
            * `.htaccess` 파일의 리디렉션 규칙 점검.
            * 워드프레스 플러그인 또는 테마 설정과의 충돌 가능성 검토.

---

## 2. FTP 및 데이터베이스 접속 (MySQL Workbench) 💻

웹사이트 파일 및 데이터베이스 관리를 위한 FTP(FileZilla) 및 MySQL Workbench 접속 시도 및 문제 해결 과정입니다.

* **FTP 접속 (FileZilla):**
    * *발생 문제:* "디렉터리 목록 조회 실패" 등 접속 불안정.
    * *참고 스크린샷 (Cafe24 FTP/DB 정보):* ![image](https://github.com/user-attachments/assets/250a48a5-acbb-498c-ba45-18987903864b)![unnamed (2)](https://github.com/user-attachments/assets/6d452fb7-ae65-4938-946c-d200f8a48655)


    * *참고 스크린샷 (파일질라 오류):* 
    * *점검 포인트:* Cafe24 패널에서 정확한 FTP/SFTP 호스트 주소, 사용자명, 비밀번호, 포트, 프로토콜 정보 확인 후 FileZilla 사이트 관리자에 재설정.
* **데이터베이스 접속 (MySQL Workbench):**
    * *초기 목표:* SSH 터널링 (`Standard TCP/IP over SSH`)을 사용하여 Cafe24의 MariaDB에 접속.
    * *참고 스크린샷 (Workbench 연결 설정 및 Cafe24 DB 정보):* ![image](https://github.com/user-attachments/assets/9ad4c5d4-f3df-40ba-9536-934725d01de7)

    * *주요 발생 오류 및 점검 과정:*
        1.  **SSH 키 지문 확인:** 첫 SSH 연결 시 서버 진위 확인 프롬프트.
            * *메시지:* "The authenticity of host ... can't be established."
           ![image](https://github.com/user-attachments/assets/9ad4c5d4-f3df-40ba-9536-934725d01de7)
            * *탐색:* "Ok"를 눌러 서버 키 수락 후 진행.
        2.  **MySQL Workbench 프로그램 충돌:**
            * *메시지:* "MySQL Workbench Unexpected Error. 외부 구성 요소에서 예외를 Throw했습니다."
           ![image](https://github.com/user-attachments/assets/d380e307-d69d-4c87-bf7b-936eb82aaefc)

            * *점검:* 사용 중인 Workbench 버전8.0.32에 SSH+MariaDB 관련 알려진 버그 확인.
            * ![image](https://github.com/user-attachments/assets/ebab14da-2e7d-4945-a704-e8651bed4bc4)

            * *해결 탐색:* 최신 안정 버전(8.0.42로 업데이트, `screenshots/image_51148a.png`)으로 재설치.
        3.  **SSH Hostname 형식 오류:**
            * *문제:* SSH Hostname에 `sftp://` 포함.
            ![image](https://github.com/user-attachments/assets/b658d1a2-d797-453e-a656-b708d257d4f8)

            * *해결 탐색:* `sftp://` 접두사 제거.
           ![image](https://github.com/user-attachments/assets/99371077-a897-4243-b208-694f24e9081f)

    * *최종 상태:* 데이터베이스 접속 성공.
    

---

## 3. 워드프레스 관리자 정보 관리 🔑

데이터베이스 직접 접근을 통해 워드프레스 관리자 정보를 확인하고 비밀번호를 변경하는 방법을 탐색합니다.

* **관리자 아이디(user_login, ID) 및 연락처 확인:**
    * *목표:* 관리자 계정의 로그인 아이디, 숫자 ID 및 저장된 연락처 정보(전화번호 등) 찾기.
    * *점검 테이블:*
        * `wp_users` (또는 사이트 설정에 따른 접두사_users): 사용자 기본 정보.
        * `wp_usermeta` (또는 접두사_usermeta): 사용자 추가 정보 (연락처 등).
        * `wp_options` (또는 접두사_options): 웹사이트 전체 설정 (사이트 대표 연락처 등).
    * *초기 혼동:* 테이블 접두사가 `wp_2_`일 것으로 추정했으나, `wp_users` 테이블이 실제 사용됨을 오류 메시지를 통해 확인. `Error Code 1146: Table 'donga4144.wp_2_users' doesn't exist`
   
* **관리자 비밀번호 변경:**
    * *목표:* 특정 사용자의 워드프레스 로그인 비밀번호 재설정.
    * *변경 대상:* `user_login = 'donga'`
  
    * *탐색 방법:* MySQL Workbench에서 `UPDATE wp_users SET user_pass = MD5('새비밀번호') WHERE user_login = '사용자아이디';` SQL 쿼리 실행.


## 결론 
* 목표 https 를 위해 SSL 인증서 발급
* 현재  페이지는 한글,  영문으로 나뉘어짐 같은 프로젝트로 구성되었기 때문에 한 도메인으로 신청해도 둘다 가능
* 10G 광 아우토반은 무료 신청서가 없음으로 아래 제품중 라이트로 신청
* ![image](https://github.com/user-attachments/assets/0c4befcd-0070-4cd6-b5f7-516cfa16b7d9)
* 가입 등록 이후
* ![image](https://github.com/user-attachments/assets/7f8dc285-84bf-40db-a4f7-d7e398342c1b)
  인증이 필요하여, 가입 메일은 내가 구성한 파일이 아니기에 모름 http 로 인증함 
![{5E6AC698-956B-47CC-8F77-B01F58EB3625}](https://github.com/user-attachments/assets/ea851e4b-2ec6-4cd6-b27e-acae254a9025)

http 인증시 
ftp 파일질라로 상위 폴더에 업로드 후 인터넷으로 확인

아래 순서로 진행하면 됨
--------------------------- 공식 안내서 ----------------------
보안인증서 신청 후 [HTTP 인증]을 신청하신 경우 하기 순서에 따라 진행해주신다면 인증이 가능합니다.


1. [나의서비스관리 > 인증서관리 > 인증서 받기]를 통해 인증파일 다운로드가 가능합니다.

2. FTP프로그램을 통해 서버에 접속하시어 [www(루트디렉토리)/.well-known/pki-validation] 폴더를

　 생성 후 [pki-validation]폴더 내 인증파일 업로드 진행 바랍니다.

※ [.well-known]폴더 생성시 . <- 온점 포함하여 생성 부탁드립니다.

※ Tomcat JSP 호스팅 HTTP인증용 파일 업로드 경로는 아래와 같습니다.

   tomcat/webapps/ROOT/.well-known/pki-validation

3. HTTP 인증 시 보안설정을 모두 OFF 해주셔야 인증이 가능합니다.

　 나의서비스관리 > 호스팅관리 > 보안관리 를 통해 설정 바랍니다.

　 다만, 국가별 웹접근 설정에서 영국만 허용해도 인증 가능한 이유는 인증기관이 영국이므로

　 인증이 가능합니다.

ⓐ 웹 방화벽 사용안함(off)

ⓑ 스팸SHIELD 사용안함(off)

ⓒ 국가별 웹접근 설정 탭에서 모든 국가를 허용 (영국만 허용해도 가능)

ⓓ FTP/Shell 접속설정을 허용

ⓔ 안티웹쉘 사용안함(off)

   (보안설정에 해당하는 부분 모두 off 부탁드립니다.)

인증 완료 후에 다시 사용함 으로 설정 변경 하셔서 이용해주시기 바랍니다.


4. 해당 가이드대로 진행하였으나 아래와 같은 오류화면이 확인되시는 경우, 인증이 정상적으로

 　완료되지않습니다. 방화벽이 파일에 액세스하는 IP 주소를 차단하지 않도록 설정해 주시기 바랍니다.

