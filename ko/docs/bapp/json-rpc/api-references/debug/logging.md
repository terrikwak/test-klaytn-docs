# 로깅 <a id="logging"></a>

## debug_backtraceAt <a id="debug_backtraceat"></a>

로깅 백트레이스 위치를 설정합니다. 백트레이스 위치가 설정되고 해당 위치에서 로그 메시지가 발생하면, 해당 로그문을 실행하던 go루틴의 스택이 `stderr`에 출력됩니다.

| 클라이언트 | 메서드 호출                                                |
|:-----:| ----------------------------------------------------- |
|  콘솔   | `debug.backtraceAt(location)`                         |
|  RPC  | `{"method": "debug_backtraceAt", "params": [string]}` |

**매개변수**

| 명칭       | 형식  | 설명                                                      |
| -------- | --- | ------------------------------------------------------- |
| location | 문자열 | 로깅 백트레이스 위치는 `<filename>:<line>`와 같이 표현됩니다. |

**리턴값**

없음

**예시**

``` javascript
> debug.backtraceAt("server.go:443")
null
```

HTTP RPC

```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_backtraceAt","params":["server.go:443"],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":null}
```


## debug_setVMLogTarget <a id="debug_setvmlogtarget"></a>

미리 컴파일된 컨트랙트 vmlog의 출력 형태를 설정합니다.  출력 형태가 파일이면, 스마트 컨트랙트에서의 `vmlog` 호출에서 발생한 로그는 `DATADIR/log/vm.log`에 쓰입니다.  `DATADIR`는 `klay`를 실행할 때 `--datadir`를 통해 지정된 디렉토리입니다.  출력 형태가 `stdout`이면, 표준 출력의 디버깅 메시지와 같이 로그가 출력됩니다.

| 클라이언트 | 메서드 호출                                                   |
|:-----:| -------------------------------------------------------- |
|  콘솔   | `debug.setVMLogTarget(target)`                           |
|  RPC  | `{"method": "debug_setVMLogTarget", "params": [number]}` |

**매개변수**

| 명칭     | 형식  | 설명                                                                   |
| ------ | --- | -------------------------------------------------------------------- |
| target | int | 출력 형태를 설정합니다. (0: 출력 없음, 1: 파일, 2: stdout, 3: 파일과 stdout) (기본 설정: 0) |

**리턴값**

| 형식  | 설명                                 |
| --- | ---------------------------------- |
| 문자열 | 출력 형태입니다.  실제 리턴값의 예시는 아래를 참고해주세요. |

**예시**

콘솔
```javascript
> debug.setVMLogTarget(0)
"no output"

> debug.setVMLogTarget(1)
"file"

> debug.setVMLogTarget(2)
"stdout"

> debug.setVMLogTarget(3)
"both file and stdout"

> debug.setVMLogTarget(4)
Error: target should be between 0 and 3
    at web3.js:3239:20
    at web3.js:6447:15
    at web3.js:5181:36
    at <anonymous>:1:1
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_setVMLogTarget","params":[3],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":"both file and stdout"}
```


## debug_verbosity <a id="debug_verbosity"></a>

로깅의 상세 정도 상한을 설정합니다. 어느 레벨 이하로 로그 메시지를 출력할 것인지 반환합니다.

(레벨 :  0=로깅 없음, 1=에러, 2=경고, 3=알림, 4=디버깅, 5=세부 사항)

`debug_vmodule`을 사용하여 각 패키지와 소스 파일의 상세 정도를 높일 수 있습니다.

| 클라이언트 | 메서드 호출                                            |
|:-----:| ------------------------------------------------- |
|  콘솔   | `debug.verbosity(level)`                          |
|  RPC  | `{"method": "debug_vmodule", "params": [number]}` |

**매개변수**

| 명칭    | 형식  | 설명                     |
| ----- | --- | ---------------------- |
| level | int | 로깅의 상세 정도를 나타내는 레벨입니다. |

**리턴값**

없음

**예시**

콘솔
```javascript
> debug.verbosity(3)
null
```
HTTP RPC
```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_verbosity","params":['3'],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":null}
```


## debug_vmodule <a id="debug_vmodule"></a>

로깅의 상세 출력 패턴을 설정합니다.

| 클라이언트 | 메서드 호출                                            |
|:-----:| ------------------------------------------------- |
|  콘솔   | `debug.vmodule(module)`                           |
|  RPC  | `{"method": "debug_vmodule", "params": [string]}` |

**매개변수**

| 명칭     | 형식  | 설명             |
| ------ | --- | -------------- |
| module | 문자열 | 로깅을 위한 모듈명입니다. |

**리턴값**

없음

**예시**

콘솔

특정 Go 패키지(디렉토리) 및 하위 디렉토리의 메시지를 확인하려면 아래와 같이 입력하세요.

```javascript
> debug.vmodule("p2p/*=5")
```

하위 디렉토리는 제외하고 특정 패키지(*예를 들어*, p2p)의 메시지로 제한하려면 아래와 같이 입력하세요.

```javascript
> debug.vmodule("p2p=4")
```

특정 소스 파일의 로그 메시지를 확인하려면 아래와 같이 입력하세요.

```javascript
> debug.vmodule("server.go=3")
```

HTTP RPC

```shell
$ curl -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"debug_vmodule","params":["p2p=4"],"id":1}' http://localhost:8551
{"jsonrpc":"2.0","id":1,"result":null}
```
