# PAM 💻
**Linux PAM 학습📝**
<br/><br/>
**날짜 : 2024-09-20**
<br/><br/>
**목표 : PAM(Pluggable Authentication Modules) 라는 리눅스 시스템에서 사용자의 인증을 담당하는 모듈을 사용하여 비밀번호를 8자리 이상으로 규제 및 학습**    
<br/><br/>
**조건📌**
- **myserver03 새로 생성 후 작업**
- **ip 고정 10.0.2.21**
<br/><br/>

PAM(Pluggable Authentication Modules)을 사용하여 리눅스 시스템에서 비밀번호를 8자리 이상으로 설정하는 규칙을 적용하려면 `pam_pwquality` 모듈을 설정해야 한다. `pam_pwquality` 모듈은 비밀번호의 길이, 복잡도, 품질을 검증하는 데 사용할 수 있으며, 다음 단계에 따라 설정할 수 있다.
<br/><br/>

**1. `libpam-pwquality` 패키지 설치**
  
대부분의 리눅스 배포판에서는 `libpam-pwquality`가 기본적으로 설치되어 있지만, 설치되지 않은 경우 다음 명령어를 사용하여 설치할 수 있다.

```bash
sudo apt update
sudo apt install libpam-pwquality
```
<br/><br/>
**2. PAM 설정 파일 수정**

`/etc/pam.d/common-password` 파일 또는 `passwd` 파일을 편집하여 비밀번호 규칙을 적용할 수 있다.

```bash
sudo nano /etc/pam.d/common-password
```
<br/><br/>
**3. `pam_pwquality` 모듈 설정**

`pam_pwquality` 모듈을 사용하여 비밀번호 길이와 복잡도를 설정한다. 다음과 같은 줄을 파일의 적절한 위치에 추가하거나 수정을 통해 설정이 가능하다.

```bash
password requisite pam_pwquality.so retry=3 minlen=8
```

![image](https://github.com/user-attachments/assets/e3e763eb-fc98-482e-8e83-b968b44cb654)


- `requisite`: 비밀번호 품질 검사를 강제
- `retry=3`: 비밀번호 입력 시도 횟수를 설정
- `minlen=8`: 최소 비밀번호 길이를 8자로 설정

<br/><br/>

예시 (`/etc/pam.d/common-password`):

```bash
password requisite pam_pwquality.so retry=3 minlen=8
password [success=1 default=ignore] pam_unix.so obscure use_authtok try_first_pass sha512
```
<br/><br/>
**4. `pwquality` 설정 파일 수정 (선택)**

`/etc/security/pwquality.conf` 파일에서 더 많은 옵션을 설정할 수 있다. 예를 들어 비밀번호의 복잡성(대문자, 숫자, 특수문자 포함 여부)이나 연속 문자 사용 제한 등을 설정할 수 있다.

```bash
sudo nano /etc/security/pwquality.conf
```

![image](https://github.com/user-attachments/assets/f08325b2-c0cf-4bc6-8233-91c1e0eae15d)



설정 예시:

```bash
minlen = 8           # 비밀번호의 최소 길이
minclass = 3         # 최소 문자 종류 (대문자, 소문자, 숫자, 특수문자 중 3가지)
maxrepeat = 2        # 연속으로 같은 문자 사용 제한
maxclassrepeat = 2   # 같은 종류의 문자 반복 제한
```

<br/><br/>
**5. 설정 적용 및 테스트**

설정을 저장하고 나서, 새로운 사용자를 생성하거나 기존 사용자의 비밀번호를 변경하여 설정이 적용되었는지 테스트해봐야 한다.

```bash
sudo passwd username
```


새로운 비밀번호를 입력할 때 8자리 이하의 비밀번호를 사용하면 오류 메시지가 출력되어야 한다.

![image](https://github.com/user-attachments/assets/154f41e2-3370-4511-b6a1-afd565e32517)

<br/><br/>
**실행결과📊**

```bash
입력 - 결과
11111111 - The password is a palindrome
aaaaaaaa - Sorry, passwords do not match.
abcdef - BAD PASSWORD: The password contains more than 2 characters of the same class consecutively
```
