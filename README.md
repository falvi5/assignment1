# assignment1
Describe getopt, getopts, sed, and awk



## getopt
  getopt를 사용하는 이유
  
    - 다양한 입력 값이 존재할 경우 사용자와 개발자의 편의를 보장하기 위함
    - 스크립트를 보다 체계쩍으로 관리할 수 있기 때문

#### 형식
  /usr/bin/getopt 를 사용
  
  short option은 -o를 사용한다
  
    - option은 나열
    - argument를 가지는 옵션은 뒤에 ':' 를 붙인다

  long option은 -l을 사용한다
      - option은 ','로 구분한다
      - argument를 가지는 옵션은 ':'를 뒤에 붙인다
 
  공통
      - 마지막에 -- "$@" 를 붙인다
      
   ex)
```shell script
#!/bin/bash

options=$( getopt -o a:bc -l help,path:,name: -- "$@" )
echo "$options"
```

```shell script
#!/bin/bash

if ! options=$( getopt -o a:bc -l help,path:,name: -- "$@" )
then 
    echo "ERROR: print usage"
    exit 1
fi

eval set -- "$options"

while true; do
    case "$1" in
        -h|--help)
            echo >&2 "$1 was triggerd!"
            shift ;;
        -p|--path)
            echo >&2 "$1 was triggered!, OPTARG: $2"
            shift 2 ;; # 옵션이 argument가 있으므로 shift 2
        -n|--name)
            echo >&2 "$1 was triggered!, OPTARG: $2"
            shift 2 ;;
        --aaa)
            echo >& "$1 was triggered!"
            shift ;;
        --)
            shift
            break
    esac
done

echo -------------
echo "$@"
```


## getopts

#### option 넣기

```shell script
#!/bin/bash

while getopts "ahd" opt; do
    case $opt in
        a)
            -a 옵션이 들어오면 할 일
            ;;
        h)
            -h 옵션이 들어오면 할 일
            ;;
        d)
            -d 옵션이 들어오면 할 일
            ;;
        \?)	# 지정이외 옵션은 이 문자로 할당된다.
            echo $@ is not valid option
            exit 0
            ;;            
    esac
done
```

#### argument 사용

```shell script
#!/bin/bash

while getopts "a:hd" opt; do
    case $opt in
        a)
            echo $OPTARG
            ;;
        h)
            echo "h"
            ;;
        d)
            echo "d"
            ;;      
    esac
done
```

  argument는
  
    option: (Verbose mode) 
    :option: (Silent mode) 로 사용한다
      - while getopts "a:hd" opt; do (a옵션은 argument(verbose mode)를 가지고, 나머지 옵션은 그냥 옵션만 가진다)
      - while getopts ":a:hd" opt; do (a옵션은 argument(silent mode)를 가지고, 나머지 옵션은 그냥 옵션만 가진다)
    
    
   Verbose mode
      
      - getopts "a:" 는 a 옵션에 콜론(:)을 붙여 인자를 요구케 함 (Verbose mode)
      - 옵션은 $opt로 들어간다.
      - argument는 $OPTARG로 들어간다.
        예: -a "hello world"
            $opt: 'a'
            $OPTARG: "hello world"
      
  Silent mode
  
  
![image](https://user-images.githubusercontent.com/94304874/142762491-22dc49a7-135e-4deb-8e40-f909cd6e8d7a.png)

    


## SED(Stream Editor)


  SED는 Stream Editor의 약자로 sed라는 명령어로 원본 텍스트 파일을 편집하는 유용한 명령어이다
  
  
  SED는 원본을 건드리지 않고 편집하기 때문에 작업이 하더라도 원본에는 영향이 없다
  
    그래서 패턴버퍼, 홀드버퍼라는 내부적으로 특수한 저장 공간을 사용한다
    
  
  #### 옵션
      * e : sed를 사용하였을 때 출력되는 값을 보여줌
      * i : 변경되는 값을 실제로 파일에 저장
      * n : 특정 값이 들어간 줄만 출력. 주로 p와 같이 사용
      * f : 스크립트를 파일로부터 읽어들이며 명령어를 지정
 
  #### 명령어
      * d  : 줄 삭제 명령어
      * a\ : 밑에 줄 삽입
      * i\ : 줄 앞에 첨가 명령어
      * c\ : 해당 줄을 변경
      * g  : 한 줄에 해당값이 여러개 있을 경우 모두 변경
      * p  : 조건에 부합하는 라인을 출력
      
  #### 사용법
  
  특정라인 출력
  
      sed -n '3,7p' <파일> # <파일>에서 3~7라인만 출력


  특정라인 제외 출력
  
      sed '3,7d' <파일> # 3~7라인 제외하고 출력


  특정라인(비연속) 출력
  
      sed -n -e '3,5p' -e '7,9p' <파일> # 3~5, 3~7라인만 출력


  치환하여 출력
  
      sed 's/1/2/g' <파일> # 1을 2로 바꿔서 출력
      
      sed 's/1/2/gi' <파일> # 1을 2로 바꿔서 출력(대소문자 무시)


  특정라인에서 치환
  
      sed '2,5 s/4/7/g' <파일> # <파일>의 2~5줄내에서 4를 7로 치환 출력


  정규식(예제1)
  
      sed '/^#\|^$\| *#/d' <파일> # "#"로 시작하거나 or 빈 줄인 라인 제거
      
  정규식(예제2)
  
      sed 's/[Zz]ip/rar/g' <파일> # zip 또는 Zip을 rar로 바꾸기


  패턴 출력
  
      sed -n '/^May 22/ p' <파일> # "May 22" 로 시작하는 라인 출력


  공백 라인 넣기
  
      sed G <파일> # 라인마다 공백라인 1줄 추가
      
      sed 'G;G' <파일> # 라인마다 공백라인 2줄 추가


  에뮬레이팅 dos2unix
  
      sed -i 's/\r//' <파일> # hidden new line 제거


  In-place 편집 및 백업 (-i 옵션)
  
      sed -i'.org' 's/this/that/gi' <파일> # 원본 수정 후 .org 확장자 파일 생성하여 백업


  쌍단어 스위칭
  
      sed 's/^.∗,.∗$/\2\, \1/g' <파일> # 특정 구분자(comma)를 기준으로 앞뒤 바꿈


  특정 조건내 변환
  
      sed '/services/ s/start/stop/g' <파일> # services를 갖는 줄에서 start를 stop으로 global하게 변환


  두개 이상 동시 변환
  
      sed -i 's/that/this/gi;s/line/verse/gi' <파일> # that을 this로 / line을 verse로 동시에 바꿈


  다른 명령어와 동시 사용
  
      ip route show | sed -n '/src/p' | sed -e 's/ */ /g' | cut -d' ' -f9 # "|"를 이용하여
      최종적으로 ip 추출("src"를 갖는 줄에서 연속된 공백을 하나로 치환하고 9번째 컬럼 값인 ip를 출력)
      
      
      
## AWK(Aho Weinberger Kernighan)

  - 개발자들의 이름 이니셜을 딴 이름이고 주로 오크라고 읽는다
  - 텍스트가 저장되어 있는 파일을 원하는대로 필터링하거나 추가해주거나 기타 가공을 통해서 나온 결과를
    행과 열로 출력해주는 프로그램이다
    
    각 라인은 record라 하고, 이 안의 단어들을 field라 한다
     
    
    
    ![img1 daumcdn](https://user-images.githubusercontent.com/94304874/142763384-4b72e2f7-cade-4aa3-ab7b-4946f4dbbb13.png)

      
      
#### 사용법 ex  
      
      ex) data.txt
      
      |name|phone|birth|sex|score|
      |:------:|:--------:|:--------:|:--:|:---:|
      |reakwon|010-1234-1234|1981-01-01|M|100|
      |sim    |010-4321-4321|1999-09-09|F|88|
      |nara   |010-1010-2020|1993-12-12|M|20|
      |yut    |010-2323-2323|1988-10-10|F|59|
      |kim    |010-1234-4321|1977-07-17|M|69|
      |nam    |010-4321-7890|1996-06-20|M|75|
      

  
  열(column)만 출력
  
  `awk '{ print $1 }' ./awk_test_file.txt`
  ```
  name
  reakwon
  sim
  nara
  yut
  kim
  nam
  ```
  
`awk '{ print $1,$2 }' ./awk_test_file.txt`
```
name phone
reakwon 010-1234-1234
sim 010-4321-4321
nara 010-1010-2020
yut 010-2323-2323
kim 010-1234-4321
nam 010-4321-7890
```  


특정 패턴이 포함된 레코드 출력

`awk '/rea/' ./awk_test_file.txt`

`reakwon 010-1234-1234   1981-01-01      M       100`


출력의 내용 첨가

`awk '{ print ("name : " $1, ", "  "phone : " $2) }' ./awk_test_file.txt`
```
name : name , phone : phone
name : reakwon , phone : 010-1234-1234
name : sim , phone : 010-4321-4321
name : nara , phone : 010-1010-2020
name : yut , phone : 010-2323-2323
name : kim , phone : 010-1234-4321
name : nam , phone : 010-4321-7890
```


특정 레코드를 검색

  * ~이상, ~이하의 레코트 출력

`awk '{ if ( $5 >= 80 ) print ($0) }' ./awk_test_file.txt`

or

`awk '$5 >= 80 { print $0 }' ./awk_test_file.txt`
```
name    phone           birth           sex     score
reakwon 010-1234-1234   1981-01-01      M       100
sim     010-4321-4321   1999-09-09      F       88
```


  * M 데이터만 출력
  
`awk '{ if ( $4 == "M" ) print ($0) }' ./awk_test_file.txt`
```
reakwon 010-1234-1234   1981-01-01      M       100
nara    010-1010-2020   1993-12-12      M       20
kim     010-1234-4321   1977-07-17      M       69
nam     010-4321-7890   1996-06-20      M       75
```


  * m이면서 80점인 레코드 출력
  
`awk '{ if ( $4 == "M" && $5 >= 80) print ($0) }' ./awk_test_file.txt`

`reakwon 010-1234-1234   1981-01-01      M       100`



  * 내장 함수
      length, substr, tolower(소문자), toupper(대문자)
      
`awk '{ print ("name leng : " length($1), "substr(0,3) : " substr($1,0,3)) }' ./awk_test_file.txt`
```
name leng : 4 substr(0,3) : nam
name leng : 7 substr(0,3) : rea
name leng : 3 substr(0,3) : sim
name leng : 4 substr(0,3) : nar
name leng : 3 substr(0,3) : yut
name leng : 3 substr(0,3) : kim
name leng : 3 substr(0,3) : nam
```

    * 반복문

```
awk '{
for(i=0;i<2;i++)
 print( "for loop :" i "\t" $1, $2, $3)
}' ./awk_test_file.txt
```

```
for loop :0     name phone birth
for loop :1     name phone birth
for loop :0     reakwon 010-1234-1234 1981-01-01
for loop :1     reakwon 010-1234-1234 1981-01-01
for loop :0     sim 010-4321-4321 1999-09-09
for loop :1     sim 010-4321-4321 1999-09-09
for loop :0     nara 010-1010-2020 1993-12-12
for loop :1     nara 010-1010-2020 1993-12-12
for loop :0     yut 010-2323-2323 1988-10-10
for loop :1     yut 010-2323-2323 1988-10-10
for loop :0     kim 010-1234-4321 1977-07-17
for loop :1     kim 010-1234-4321 1977-07-17
for loop :0     nam 010-4321-7890 1996-06-20
for loop :1     nam 010-4321-7890 1996-06-20
```

    * BEGIN, END 패턴과 변수사용
```    
    awk '
 BEGIN {
  sum = 0
  cnt = -1
 }

 {
  sum += $5
  cnt++
 }

 END {
  avg = sum/cnt
  print ("sum :" sum ", average :" avg)
 }' ./awk_test_file.txt
```  

8
