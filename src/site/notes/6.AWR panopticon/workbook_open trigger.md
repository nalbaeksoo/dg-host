---
{"dg-publish":true,"permalink":"/6.AWR panopticon/workbook_open trigger/","dgPassFrontmatter":true}
---


2023-05-17 / 22:12


```
Private Sub Workbook_Open()
    ' 엑셀 파일이 열릴 때 수행할 작업을 여기에 작성합니다.
    ' 예를 들어, 특정 시트의 데이터를 초기화하는 등의 작업을 수행할 수 있습니다.
    
    ' 예제: Sheet1의 A1 셀 값을 초기화합니다.
    Sheets("Sheet1").Range("A1").Value = ""
    
    ' 추가적인 작업을 수행할 수 있습니다.
    ' ...
    
End Sub

```

위의 예제에서는 Workbook_Open 이벤트 핸들러 안에 원하는 작업을 작성하면 됩니다. 예를 들어, 특정 시트의 데이터를 초기화하는 작업을 수행하거나, 특정 셀에 값을 설정하는 등의 작업을 할 수 있습니다. 이 코드는 Excel 파일이 열릴 때마다 실행되며, 해당 작업이 자동으로 수행됩니다.

참고로, Workbook_Open 이벤트 핸들러는 해당 이벤트가 발생했을 때 실행되는 코드를 포함하는 "ThisWorkbook" 모듈에 작성해야 합니다. 이벤트 핸들러를 작성하는 방법은 다음과 같습니다:

1.  엑셀 파일을 엽니다.
2.  VBA 편집기를 엽니다. [개발] 탭의 [Visual Basic] 버튼을 클릭하면 VBA 편집기가 열립니다.
3.  VBA 편집기에서 "Project Explorer" 창을 확인합니다. 보통 왼쪽에 위치합니다.
4.  "Project Explorer" 창에서 "VBAProject (파일 이름)"을 찾습니다.
5.  해당 프로젝트 아래에 있는 "Microsoft Excel Objects"를 확장합니다.
6.  "ThisWorkbook"를 더블 클릭하여 모듈을 엽니다.
7.  열린 모듈에 위의 예제 코드를 붙여넣습니다.
8.  파일을 저장하고 VBA 편집기를 닫습니다.

이제 Excel 파일을 열 때 Workbook_Open 이벤트가 트리거되어 예제 코드가 실행됩니다.