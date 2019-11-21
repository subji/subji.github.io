---
title: "List or Map value print just string (vscode debugger에서 list 또는 map이 문자열로만 나올 경우)"
date: 2019-11-21
categories: posts
tags: ["vscode"]
---
스프링 프로젝트 중 디버깅이 필요해서 vscode 에서 지원하는 디버깅을 사용했다.

그런데.. list 나 map 이 값으로 조회되는게 아니라 문자열로 "arraylist@xxx size=0" 이런식으로 나오는게 아닌가??!

그래서 열심히 구글링을 해보니 튜토리얼에 제공이 되어있어 쉽게 해결할 수 있었다.
```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "(lldb) Launch",
      "type": "cppdbg",
      "request": "launch",
      "program": "${workspaceFolder}/helloworld.out",
      "args": [],
      "stopAtEntry": true,
      "cwd": "${workspaceFolder}",
      "environment": [],
      // set debugger config for printing map or list value
      // ========================
      "externalConsole": true,
      "MIMode": "lldb",
      "logging": {
        "trace": true,
        "traceResponse": true,
        "engineLogging": true
      }
      // ========================
    }
  ]
}
```
표시된 곳을 디버거 툴 옆 톱니바퀴를 눌러 나오는 configuration 에 입력해주면 된다.