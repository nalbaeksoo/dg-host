---
{"dg-publish":true,"permalink":"/6.AWR panopticon/timer 사용법/","dgPassFrontmatter":true,"noteIcon":""}
---


2023-05-13 / 00:53

```
Sub MeasureStepTime()
    Dim startTime As Double
    Dim endTime As Double
    Dim stepTime As Double
    Dim step As Integer
    
    For step = 1 To 3
        startTime = Timer
        ' 각 단계의 코드
        endTime = Timer
        stepTime = endTime - startTime
        MsgBox "단계 " & step & " 수행 시간: " & stepTime & "초"
    Next step
End Sub
```