# Homework-1
---
## **getopts 명령어**
 
+ 쉘에서 명령을 실행할 때 옵션을 사용하는데 스크립트 파일이나 함수를 실행할 때 또한 옵션을 사용할 수 있음.
+ 이 때 스크립트 내에서 직접 옵션을 해석해서 사용해야 하는데 이 작업을 쉽게 도와주는 명령어가 ***getopts***
+ ***getopts*** 명령을 이용하지 않고 직접 옵션을 처리하려면 옵션을 처리에만 스크립트가 복잡해질 수 있음.

**사용방법**

#### `getopts optstring varname [args...]`


  `$ command -a xyz -b -c hello world`
  
####  option argument : xyz   /   option string : -a, -b, -c

+ 명령에서 `-a`,`-b`,`-c` 세 개의 옵션을 사용한다면 ***getopts*** 명령에 설정하는 ***optstring*** 값은 `abc`임
+ shell이 처음 실행되면 옵션을 가리키는 ***$OPTIND*** 값은 1을 가리키고 있음
+ ***getopts***명령이 실행될 때마다 다음 옵션을 가리키게 됨


### *option string*


![image](https://user-images.githubusercontent.com/43903354/142414010-f98cd1e3-4d5f-4f97-bbfc-03c231190f3d.png)
![image](https://user-images.githubusercontent.com/43903354/142414094-2ae8f5e6-afdf-4ace-88f2-9e843b867183.png)

- 처음 ***$OPTIND*** 값은 1로 -a를 가리킴


![image](https://user-images.githubusercontent.com/43903354/142414230-fff800e4-a1cc-4f4c-a77a-259f5c3b0987.png)

- ***getopts*** 명령을 실행할 때마다 ***opt*** 변수값과 ***OPTIND*** 값이 변경됨
- 다음 옵션인 b의 index값은 2임



![image](https://user-images.githubusercontent.com/43903354/142414458-e04acfba-7f9b-43c8-9782-8e7f057dee79.png)

- 다음 옵션 c는 b와 붙여 쓰여있기 때문에 같은 ***OPTIND***값을 가짐
- ( 이는 bash에서의 경우고 sh의 경우에 b의 index는 3임 )


![image](https://user-images.githubusercontent.com/43903354/142414613-4efdf9c1-d910-4d3c-b074-162ec09d9161.png)



- 위 예시들과 달리 "$@"를 생략한 `$ getopts abc opt`로 사용하였는데 args 부분을 생략하면 default 값은 "$@"임

![image](https://user-images.githubusercontent.com/43903354/142415239-8f2097f0-0695-4801-9fa0-f8bfa16b7cf2.png)
![image](https://user-images.githubusercontent.com/43903354/142415364-8e77abf0-68f7-4af4-9aaf-1271b182b1b5.png)

- ***optstring*** 부분을 읽어 냈어도 아직 ***optstring***이 존재 하므로 ***shift***를 사용하며 나머지 명령 인수만 남게 함


### *option argument*

- 옵션은 옵션인수를 가질 수 있는데 옵션 스트링에서 해당 옵션 문자 뒤에 `:`을 붙이면 됨
- 그러면 ***getopts***명령은 옵션인수 값을 `$OPTARG` 변수에 설정해줌

![image](https://user-images.githubusercontent.com/43903354/142416753-e79c0eb7-cafd-4a43-b307-74114a24e02a.png)
![image](https://user-images.githubusercontent.com/43903354/142416791-7587d9fa-b5f6-4f52-989a-ef09684fda89.png)

- 옵션 `a`가 옵션인수를 가지므로 옵션 스트링으로 `a:` 를 설정

![image](https://user-images.githubusercontent.com/43903354/142416848-f654e161-bba8-48c9-8921-cecdffc583ce.png)

![image](https://user-images.githubusercontent.com/43903354/142417035-e67c26f8-674b-4d98-94c6-2665e2053aa2.png)

- 옵션 `b`와 `c`는 옵션인수를 가지지 않으므로 ***$OPTARG***가 비어있음


**option 종류**

+ short 옵션
+ long 옵션


### **1) short 옵션의 특징**

+ -가 하나만 붙어있는 것들을 옵션으로 처리함


short 옵션은 다음과 같이 여러 가지 방법으로 사용 가능

```bash
$ command -a -b -c

$ command -abc
$ command -b -ca
# 옵션을 붙여서 사용할 수 있으며 순서가 바뀌어도 됨

$ command -a xxx -b -c yyy
# 옵션인수를 가질 수 있음

$ command -axxx -bcyyy
# 옵션인수를 옵션에 붙여 쓸 수 있음

$ command -a -b -- -c
# 옵션 구분자 '--'가 올경우 우측에 있는 값은 옵션으로 해석하면 안됨
```


### **2) long 옵션의 특징**

+ `--posix`,`--warning level` 와 같은 형태로 사용되는 long 옵션은 short옵션과는 달리 붙여 쓸 수가 없기에 
  
  사용방법이 간단하며 직접 해석해서 처리하는 것이 어렵지 않음

+ 하지만 long 옵션을 처리하는 때에 주의 할것이 있음
`$ ./test.sh -a aaa --posix --long 123 -b --warning=2 -- hello world`

위와 같은 명령문을 ***getopts***로 처리하면 두 가지 문제점이 있음
1) ***getopts***는 하나의 문자를 옵션으로 보기 때문에 `p`,`o`,`s`,`i`,`x` 가 모두 붙여쓰기한 옵션명으로 인식 하게됨
2) 위의 `--long` 옵션과 같이 `123` 옵션인수를 사용하게 되면 그 이후의 옵션 `-b` 는 ***getopts*** 에 의해 인식이 되지 않음

때문에 ***getopts*** 로 **short, long** 옵션을 동시에 처리는 매우 어려움 / 그래서 먼저 **long** 옵션을 처리하고 **long** 옵션 부분을 제거해낸 나머지를 ***getopts***에 넘겨주어 **short** 옵션을 처리하는 방식을 사용함

---

### **Error reporting**

getopts 명령은 error reporting을 하는 두 개의 모드를 제공

|**Verbose mode**||
|---|---|
|invalid옵션 사용|***opt***값을 `?`문자로 설정 / ***OPTARG***값은 unset / 오류 메시지 출력|
|옵션인수 값을 제공 X|***opt***값을 `?`문자로 설정 / ***OPTARG***값은 unset / 오류 메시지 출력|

|**Silent mode**||
|---|---|
|invalid옵션 사용|***opt***값을 `?`문자로 설정 / ***OPTARG*** 값은 해당 옵션 문자로 설정|
|옵션인수 값을 제공 X|***opt***값은 `:`문자로 설정 / ***OPTARG*** 값은 해당 옵션 문자로 설정|

- default로 **verbose mode**임
- 하지만 옵션과 관련된 오류 메시지가 표시되기 때문에 스크립트를 배포할 때는 **silent mode**를 이용
- **silent mode**를 설정하려면 옵션 스트링 맨 앞에 `:` 문자를 추가
- ex) `getopts ":a:" opt`


### **주의할 점**

- ***OPTIND***, ***OPTARG*** 변수는 local 변수가 아니기 때문에 필요할 경우 함수 내에서 local로 설정해서 사용해야 함

- `$ command -a -b -c` 옵션 스트링이 'a:bc' 이면 `-a`는 옵션 인수를 가지는데 이처럼 옵션 인수를 설정해주지 않으면 `-b`가 `-a`의 옵션인수가 됨

- `$ command -a foo.c -b -c` 파일명이나 기타 스트링은 맨 마지막에 와야함 그렇지 않을 경우 이후 옵션들은 인식 X / 즉, `-b -c` 옵션은 인식 X

---

## **getopt 명령어**

+ `getopt` 명령은 `getopts` 명령과 비슷한데 `/usr/bin/getopt` 에 위치한 외부 명령임 / `getopts` 는 **built-in**
+ `getopt` 명령은 기본적으로 short, long 옵션을 모두 지원
+ 옵션 인수를 가질 경우 `getopts`와 마찬가지로 `:`문자를 사용함

```bash
$ getopt -o a:bc
# short 옵션 지정은 -o 옵션으로 함

$ getopt -l help,path:,name:
# long 옵션 지정은 -l 옵션으로 하고 옵션명은 ','로 구분함
# 명령 라인에서 옵션 인수 사용은 "--name foo" 또는 "--name=foo" 두 가지 모두 가능

$ getopt -o a:bc -l help,path:,name: -- "$@"
# 명령 마지막에는 -- 와 함께 "$@" 를 붙임
```
+ 설정하지 않은 옵션이 사용되거나 옵션 인수가 빠지면 오류 메시지를 출력 해줌

```bash
#!/bin/bash


options=$( getopt -o a:bc -l help,path:,name: -- "$@" )
echo "$options"
---------------------------------------------------------


$ ./test.sh -x
getopt: invalid option -- 'x'

$ ./test.sh --xxx
getopt: unrecognized option '--xxx'

4 ./test.sh -a
getopt: option requires an argument -- 'a'

$ ./test.sh --name
getopt: option '--name' requires an argument

```


+ 그리고 `getopt` 명령의 특징은 `getopts` 에서는 힘들게 분류하여 사용했던 옵션들을 사용하기 좋게 정렬해줌
```bash
#!/bin/bash

options=$( getopt -o a:bc -l help,path:,name: -- "$@" )
echo "$options"
-------------------------------------------------------


$ ./test.sh -a123 -bc hello.c
-a '123' -bc -- 'hello.c'
# -a123 이 -a와 '123'으로 분리
# -bc 는 -b 와 -c로 분리
# 옵션이 아닌 hello.c는 '--' 뒤로 이동

$ ./test.sh --name foo --path=/usr/bin
--name 'foo' --path '/usr/bin' --
# --path=/usr/bin 이 --path '/usr/bin' 으로 분리

$ ./test.sh -a123 -bc hello.c -- -x --yyy
-a '123' -b -c -- 'hello.c' '-x' '--yyy'
# 중간에 파일명이 와도 잘 처리해줌
# '--' 도 알맞게 잘 처리해줌

```

---

## **awk 명령어**

`awk [OPTION] [awk program] [ARGUMENT]`

+ 파일로부터 레코드를 선택하고, 선택된 레코드에 포함된 값을 조작하거나 데이터화 함
+ awk라는 이름은 기능을 디자인한 사람들 **A**ho, **W**einberger, **K**ernighan 의 이니셜을 조합하여 만들었음

### **awk 명령 사용 용도**

1) 텍스트 파일의 전체 내용 출력
2) 파일의 특정 필드 출력
3) 특정 필드에 문자열을 추가해서 출력
4) 패턴이 포함된 레코드 출력
5) 특정 필드에 연산 수행 결과 출력
6) 필드 값 비교에 따라 레코드 출력

### **awk 명령어 옵션**


|**option 종류**||
|---|---|
|-F |필드 구분 문자 지정|
|-f |awk program 파일 경로 지정|
|-v |awk program에서 사용될 특정 variable값 지정|

*awk program* : f 옵션이 사용되지 않은 경우, awk가 실행할 awk program 코드 지정함

*ARGUMENT* : 입력 파일 지정 또는 variable 값 지정

### **awk 명령 예시**

1) 텍스트 파일의 전체 내용 출력

![image](https://user-images.githubusercontent.com/43903354/142623584-048338ae-3e03-4869-a323-1d0669f6f2e8.png)

2) 파일의 특정 필드 출력

![image](https://user-images.githubusercontent.com/43903354/142623623-559da6b2-2b3c-482e-9ca2-fc94fb643541.png)
![image](https://user-images.githubusercontent.com/43903354/142623633-ef3572cf-c5a9-4ae0-8eff-9701944fae30.png)
![image](https://user-images.githubusercontent.com/43903354/142623647-3460b926-70ca-407d-ae15-2f3865d1ce9f.png)
![image](https://user-images.githubusercontent.com/43903354/142623671-c16bdfa6-eaaa-474c-ae7f-12bb5c6c2a59.png)

3) 특정 필드에 문자열을 추가해서 출력

![image](https://user-images.githubusercontent.com/43903354/142623742-d505afa3-0b32-4b37-8330-3c798cd0c66d.png)

4) 패턴이 포함된 레코드 출력

![image](https://user-images.githubusercontent.com/43903354/142623797-17d9824e-0bfa-4c1e-910d-4311fc573d14.png)

5) 특정 필드에 연산 수행 결과 출력

![image](https://user-images.githubusercontent.com/43903354/142623895-067b8fb5-2089-4cef-a833-485f2f8a9530.png)

6) 필드 값 비교에 따라 레코드 출력

![image](https://user-images.githubusercontent.com/43903354/142623974-973e586f-39bf-4b16-a527-001c752e55ba.png)

---


## **sed 명령어**

`sed -n -e 'command' [input file]`

+ SED는 **S**tream **Ed**itor의 약자
+ 원본 텍스트 파일을 편집하는 명령어
+ vi 편집기와 달리 실시간 저장이 불가능
+ 내부적으로 저장 공간 버퍼를 사용 (**패턴 버퍼**, **홀드 버퍼**) // 파일의 내용을 **패턴 버퍼**에 가져온 후 변형과 추가를 위해 **홀드 버퍼**를 

|vi에디터|sed|
|---|---|
|실시간 저장 가능|실시간 저장 불가능|
|원본을 건드리며 변경|원본을 건드리지 않으며 변경 가능|

|**option 종류**||
|---|---|
|-n|sed는 내용을 자동적으로 출력 해주는데 `-n`옵션을 사용시 자동출력 X|
|-e|우리가 사용하는 command로 텍스트 파일을 가공해줌|

1) **특정 행을 출력 -p (print)**

    **p**는 **print**를 의미
  
  + 전체 내용 출력 : command => `/$/p` 또는 `1,$p`
  ![image](https://user-images.githubusercontent.com/43903354/142633721-cea3104e-a2c4-461d-8234-b7f31fe6d39e.png)

  + 첫번째 줄 출력 : command => `1p`
  ![image](https://user-images.githubusercontent.com/43903354/142633972-2dad814b-c7e4-42e4-8905-b32ab8c075fc.png)
  
  + **start ~ end** 줄까지 출력
  
  ![image](https://user-images.githubusercontent.com/43903354/142634371-5e38b076-d0c1-4e9a-9603-586f53306273.png)
  
  + 특정줄에서 마지막 줄까지 출력
  ![image](https://user-images.githubusercontent.com/43903354/142634636-edc9777c-5bbd-4ac7-91e7-89eaa16c3cb8.png)

  + 다중 command 사용
  ![image](https://user-images.githubusercontent.com/43903354/142634868-89b07a35-21eb-4a0f-9fca-d499bead3e28.png)

  + 특정 문자열이 있는 줄 출력
  ![image](https://user-images.githubusercontent.com/43903354/142635431-32d2759a-5d12-44fd-bc9b-12a76702dbc4.png)
  
2) **특정 행 삭제 -d (delete)**

  + 2~3번 째 줄을 삭제하고 나머지 모든 내용 출력
  ![image](https://user-images.githubusercontent.com/43903354/142635784-6baf2d12-eafd-4bf5-b8f6-56e9bd76fd15.png)
  
3) **단어 치환 -s (Substitute)**
  
  + 기본적인 특정 단어 치환 ( s는 substitute의 약자 그리고 g는 global의 약자로 전체에 적용됨을 의미 )
  ![image](https://user-images.githubusercontent.com/43903354/142636219-6d01f334-e03c-4c8e-9fa4-fe7130900671.png)
  
  + 특정 단어로 시작 혹은 끝나는 단어를 포함하는 라인의 문자열 치환
  ![image](https://user-images.githubusercontent.com/43903354/142636688-e53a0694-a95d-447f-b3dd-cd03b258948b.png)
  ![image](https://user-images.githubusercontent.com/43903354/142636948-9c4116db-19a5-496f-a9b9-43b7422868ee.png)
  
4) **문자열 추가 -a, i (append, insert)
  ```
  /찾을 문자열/a\다음 줄에 추가할 문자열
  /찾을 문자열/i\위에 삽입할 문자열
  ```
  
  ![image](https://user-images.githubusercontent.com/43903354/142637779-138a2151-2a0f-498a-9d48-7bf78740e9bb.png)
  
5) **특정 행의 내용을 전부 교체 -c (change)**
`/바꿀 행이 포함한 문자열/c\바꿀 행의 내용

  ![image](https://user-images.githubusercontent.com/43903354/142638285-113a11c1-11e9-4728-ae96-b5ff5c49b44c.png)
  
6) **특정 행에 파일의 내용을 추가 -r (read)**

  + 100으로 끝나는 줄의 아랫줄에 텍스트 파일의 내용을 추가
  ![image](https://user-images.githubusercontent.com/43903354/142638836-c03dbeb9-6cda-4d5e-b63a-dae0ae457e97.png)

