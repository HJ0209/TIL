# instance 사용하기

## 1. Amazon Machine Image 선택

![image-20200115150117776](C:%5CUsers%5CHPE%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20200115150117776.png)

Linux2 AMI로 선택



## 2. instance 유형 선택

![image-20200115150146109](C:%5CUsers%5CHPE%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20200115150146109.png)

프리티어 사용가능 선택 (무료)

일반적으로는 `t3a.micro` 이상 추천(but 과금)



## 3. 세부정보 (생략가능)

![image-20200115150511216](C:%5CUsers%5CHPE%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20200115150511216.png)

인스턴스 개수 1개



## 4. 스토리지 추가 (생략가능)

![image-20200115150602402](C:%5CUsers%5CHPE%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20200115150602402.png)

범용으로 선택 (다른 것은 과금)

프리티어 사용 가능 고객은 최대 30GB으 범용SSD 또는 마그네틱 스토리지 사용



## 5. 태그 추가 (생략가능)

![image-20200115150740934](C:%5CUsers%5CHPE%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20200115150740934.png)

태그 설정 가능



## 6. 키페어 설정

시작하기 클릭

![image-20200115150907717](C:%5CUsers%5CHPE%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20200115150907717.png)

* 새 키 페어 생성 > 이름 입력 > 다운로드 (.pem) 파일 다운

* 파일은 한번만 다운 받을 수 있으므로 꼭 다운받기
* 되도록 /user/.ssd 폴더에 저장하는 것 추천



## 7. instance 생성

* pending 상태로 나타남

![image-20200115151033150](C:%5CUsers%5CHPE%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20200115151033150.png)



* running
* ![image-20200115152204309](C:%5CUsers%5CHPE%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20200115152204309.png)