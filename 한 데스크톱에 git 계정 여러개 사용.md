# 한 데스크톱에 git 계정 여러개 사용

>  회사 계정, 개인 계정 모두 한 데스크톱에 연결해두고 싶은 경우
>
> 참고: [[Git\] 컴퓨터에 github 계정 여러개 연동하기 : ssh-key 발급, 등록 (tistory.com)](https://computer-science-student.tistory.com/367)	

​																					

## 1. ssh-key 생성

윈도우는 git, OS X 환경은 터미널에서 아래 명령어 차례로 입력

```bash
$ cd ~/.ssh (꼭 여기서 안해도 됨)
$ ssh-keygen -t rsa -C "userA@my_email.com" -f "id_rsa_userA"
$ ssh-keygen -t rsa -C "userB@my_email.com" -f "id_rsa_userB"
```

- id_rsa_userA/id_rsa_userB라는 파일명으로 저장하겠다는 의미
- rsa: 암호화 뿐만 아니라 전자서명이 가능한 최초의 알고리즘. 소인수분해의 난해함에 기반해, 공개키만 가지고는 개인키를 쉽게 짐작할 수 없도록 디자인되어있는 알고리즘 

- 결과

  ````
  Generating public/private rsa key pair
  Enter file in which to save the key (/C/Users/저장경로/파일명)
  ```
  Enter passphrase(empty for no passphrase)
  ````

  - ssh-key를 어디에 저장할 것인지 말하는 것.
  - 그냥 엔터를 치면 기본적으로 지정된 위치와 파일명으로 저장
  - 파일명의 경우, 계정을 구분지을 수 있는 것으로 설정하는 것이 좋음 (userA / userB처럼)
  - passphrase 그냥 안쳐도 됨

## 2. 생성한 키 등록

```bash
$ ssh-add ~/.ssh/id_rsa_userA
$ ssh-add ~/.ssh/id_rsa_userB
```



## 3. ssh-key 적용

- .ssh 폴더에 기존 config 파일 만듦

  ```bash
  $ vim config
  ```

- config 파일 열기

  ```bash
  $ vi config
  ```

- config 파일에 아래 내용 (그대로) 작성

  - 리눅스에서 일반적으로 ssh key를 생성하면, id_rsa 같은 파일명에 생성된다. 이렇게 기본값을 생성했을 경우, 별다른 설정없이 github에 자신의 public key를 등록해준다면 문제없이 github과 연동이 되지만, 여러 개의 ssh key들을 생성했고, 관리의 편의성을 위해 파일명을 바꾸었을 경우에는 접속이 잘 되지 않는다. 
  
  ```bash
  Host github.com-userA
  	HostName github.com
  	User git
     	IdentityFile ~/.ssh/id_rsa_userA
      
  Host github.com-userB
  	HostName github.com
  	User git
     	IdentityFile ~/.ssh/id_rsa_userB
  ```
  
  

## 3. ssh key 깃 사이트에 적용

```bash
$ cat id_rsa_userA.pub
$ cat id_rsa_userB.pub
```

- userA 계정 내 settings => SSH and GPG keys => New SSH key 들어가서 제목 짓고, 붙여넣기
- userB에도 똑같이 적용

## 4. remote 연결

- code => SSH 클릭

```bash
$ git remote add origin git@github.com-userA:계정이름/저장소이름.git
$ git remote add origin git@github.com-userB:계정이름/저장소이름.git
```

- 처음 연결할 때 저렇게 하거나, git clone 시 적용

- commit 전에, 아래 코드를 통해 로컬 저장소에 알림/ 전역으로 설정하면 또 꼬인다. 

  ```bash
  $ git config --local user.name "username"
  $ git config --local user.email "email@email.com"
  ```

  

