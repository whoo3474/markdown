
셸 스크립트 프로그래밍 응용을 해보겠습니다.
기초에서는 명령어를 어떤식으로 작성하는지, 셸 스크립트를 어떻게 작성하고 실행시키는지 설명했습니다.


### 1) 변수 선언 및 할당
변수는 쉘에 값을 저장하는 데 사용됩니다. 
파일 경로, 구성 값 및 스크립트 전체에서 참조할 수 있는 기타 데이터를 저장하는 데 사용할 수 있습니다.
```shell
VARIABLE_NAME=value
```

예를 들어 "filename"이라는 변수를 만들고 "example.txt" 값을 할당하려면 아래와 같이 사용합니다.
```shell
filename=example.txt
```

변수는 다른 변수나 문자열과 결합하여 사용할 수도 있습니다. 
이를 변수 확장이라고 합니다. 변수 확장에는 ${}, $와 ` `를 사용합니다.

예를 들어, 다음 코드는 name과 age 변수를 결합하여 메시지를 출력합니다.

```shell
name="John"
age=30
echo "My name is ${name} and I am ${age} years old"
```
위의 코드에서, "My name is ${name} and I am ${age} years old"는 변수 name과 age를 결합하여 메시지를 출력합니다. 
${}는 변수를 확장하여 변수 이름과 값을 결합합니다.

### 2) 조건문
조건문은 특정 조건이 참인지 거짓인지 테스트하는 데 사용됩니다. 
스크립트에서 결정을 내리는 데 사용되며 실행 흐름을 제어하는​​데 사용할 수 있습니다. 
쉘 프로그래밍에서 가장 일반적으로 사용되는 조건문은 if 문입니다.
```shell
if [ 조건문 ]
then
   조건이 참이라면 작동되는 명령어
else
   조건이 거짓이라면 작동되는 명령어
fi
```

예를 들어 작업을 수행하기 전에 파일이 있는지 확인하려면 아래와 같이 사용할 수 있습니다.

```shell
if [ -f /path/to/file ]
then
    echo "File exists"
else
    echo "File does not exist"
fi
```

위의 코드에서, [ -f /path/to/file ]는 파일이 존재하는지 여부를 확인하는 조건문입니다. 
-f는 해당 파일이 일반 파일인지 여부를 확인하는 옵션입니다. 
then 블록은 파일이 존재하는 경우 "File exists"를 출력합니다. else 블록은 파일이 존재하지 않는 경우 "File does not exist"를 출력합니다.

if 문은 여러 개의 조건을 조합할 수도 있습니다.
 이 경우 &&와 || 연산자를 사용하여 조건을 결합합니다. &&는 AND 연산자이며, ||는 OR 연산자입니다.

```shell
if [ -f /path/to/file ] && [ -r /path/to/file ]
then
    echo "File exists and is readable"
else
    echo "File does not exist or is not readable"
fi
```
위의 코드에서, && 연산자를 사용하여 두 가지 조건을 모두 만족해야 then 블록이 실행됩니다. 
즉, 파일이 존재하고 읽을 수 있는 경우 "File exists and is readable"를 출력합니다. 
그렇지 않으면 else 블록이 실행되며 "File does not exist or is not readable"를 출력합니다.


### 3) 반복문
반복문은 파일 목록이나 숫자 목록과 같은 일련의 값을 반복하는 데 사용됩니다. 
쉘 프로그래밍에서 가장 일반적으로 사용되는 반복문은 for입니다.
```shell
for 변수 in 목록
do
    명령어
done
```
위의 구문에서 변수는 목록에서 하나씩 추출되는 각 항목을 저장하는 변수입니다. 
목록은 for 문에서 반복하려는 항목의 목록을 지정합니다. 
명령어는 각 항목에 대해 실행될 명령어 블록입니다.

예를 들어 파일 목록을 반복하고 각 파일의 이름을 인쇄하려면 다음을 사용할 수 있습니다.

```shell
for file in /path/to/files/*; do
    echo $file
done
```

for 문은 여러 목록에 대해 반복할 수도 있습니다. 이 경우, 목록은 공백으로 구분됩니다.

```shell
for name in John Alice Bob
do
    echo "Hello $name"
done
```
위의 코드에서, for 문은 John, Alice, Bob 세 가지 이름에 대해 반복하며, 
각 이름에 대해 "Hello"라는 문자열과 함께 인사말을 출력합니다.

또한, for 문은 목록을 동적으로 생성할 수도 있습니다. 이 경우 명령어 치환을 사용하여 목록을 생성할 수 있습니다.

```shell
for file in $(ls /var/log/*.log)
do
    echo $file
done
```

### 4)함수
함수는 스크립트 전체에서 재사용할 수 있는 명령 집합을 함께 그룹화하는 데 사용됩니다. 
스크립트를 더 모듈화하고 읽기 쉽게 만드는 데 사용할 수 있습니다. 
함수를 만들려면 다음 구문을 사용합니다.
```shell
function_name() {
    명령어 작성
}
```

예를 들어 파일이 존재하는지 확인하는 함수를 만들려면 다음을 사용할 수 있습니다.
```shell
file_exists() {
    if [ -f $1 ]; then
        return 0
    else
        return 1
    fi
}
```