---
{"dg-publish":true,"permalink":"/6.AWR panopticon/vba filehandling/","dgPassFrontmatter":true,"noteIcon":""}
---


2023-05-15 / 15:18

- 특정 directory의 file명 출력
```
Sub ListTempFilesInFolder()
    Dim FolderPath As String
    Dim Filename As String
    Dim i As Integer
    
    ' 특정 폴더의 경로 지정
    FolderPath = "C:\YourFolderPath\"
    
    ' 지정한 폴더의 파일 목록 가져오기
    Filename = Dir(FolderPath & "temp*.*", vbNormal)
    
    ' 파일 목록 출력
    i = 1
    Do While Filename <> ""
        ' 파일명 출력
        Cells(i, 1).Value = Filename
        
        ' 다음 파일 가져오기
        Filename = Dir()
        
        ' 다음 행으로 이동
        i = i + 1
    Loop
End Sub

```

- 디렉토리 생성
```
Sub CreateFolder()
    Dim folderPath As String
    folderPath = "C:\경로\새로운_폴더" ' 생성할 폴더의 경로를 지정합니다.
    
    If Dir(folderPath, vbDirectory) = "" Then ' 폴더가 존재하지 않으면
        MkDir folderPath ' 폴더를 생성합니다.
        MsgBox "폴더가 생성되었습니다."
    Else
        MsgBox "이미 폴더가 존재합니다."
    End If
End Sub

```

