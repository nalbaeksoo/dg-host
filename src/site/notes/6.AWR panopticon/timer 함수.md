---
{"dg-publish":true,"permalink":"/6.AWR panopticon/timer 함수/","dgPassFrontmatter":true}
---


2023-05-13 / 00:53

```
Sub MeasureStepTime()
    Dim startTime As Double
    Dim endTime As Double
    Dim stepTime As Double
    
    ' 첫 번째 단계
    startTime = Timer
    ' 첫 번째 단계의 코드
    endTime = Timer
    stepTime = endTime - startTime
    MsgBox "첫 번째 단계 수행 시간: " & stepTime & "초"
    
    ' 두 번째 단계
    startTime = Timer
    ' 두 번째 단계의 코드
    endTime = Timer
    stepTime = endTime - startTime
    MsgBox "두 번째 단계 수행 시간: " & stepTime & "초"
    
    ' 세 번째 단계
    startTime = Timer
    ' 세 번째 단계의 코드
    endTime = Timer
    stepTime = endTime - startTime
    MsgBox "세 번째 단계 수행 시간: " & stepTime & "초"
End Sub
```