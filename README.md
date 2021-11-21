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

      








