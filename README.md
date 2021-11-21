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

    











