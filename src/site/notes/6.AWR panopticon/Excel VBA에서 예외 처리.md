---
{"dg-publish":true,"permalink":"/6.AWR panopticon/Excel VBA에서 예외 처리/","dgPassFrontmatter":true}
---


Excel VBA에서 예외 처리를 하는 일반적인 방법은 다음과 같습니다.

1.  On Error 구문: VBA 코드에서 예외가 발생했을 때 실행할 특정 코드를 지정하는 구문입니다. 예외가 발생하면 해당 코드가 실행됩니다.

vbnetCopy code

```
On Error GoTo 에러처리

' 예외가 발생할 가능성이 있는 코드

에러처리:
' 예외가 발생했을 때 실행할 코드

```


2.  Try-Catch 구문: 다른 프로그래밍 언어에서 많이 사용되는 예외 처리 방법입니다. VBA에서는 기본적으로 지원하지 않지만, 클래스 모듈을 사용하여 구현할 수 있습니다.

vbnetCopy code

```
' 예외를 처리할 클래스 모듈을 생성합니다.
Public Class MyException
    Public Message As String
End Class

' 예외 처리할 코드
Sub 예외처리()
    Dim ex As MyException
    
    On Error GoTo 에러처리
    
    ' 예외가 발생할 가능성이 있는 코드
    If Range("A1").Value = "" Then
        Set ex = New MyException
        ex.Message = "A1 셀이 비어있습니다."
        Err.Raise vbObjectError, , ex.Message
    End If
    
    Exit Sub

에러처리:
    ' 예외가 발생했을 때 실행할 코드
    MsgBox Err.Description
End Sub
```

위의 예시에서는 클래스 모듈을 사용하여 MyException 클래스를 생성하고, 예외가 발생할 때는 Err.Raise 구문을 사용하여 예외를 발생시켰습니다. 예외가 발생하면 해당 코드가 Catch 구문으로 이동하여 예외를 처리합니다.

이외에도 예외 처리 방법은 다양합니다. 각 상황에 맞게 적절한 예외 처리 방법을 선택하여 사용하시면 됩니다.