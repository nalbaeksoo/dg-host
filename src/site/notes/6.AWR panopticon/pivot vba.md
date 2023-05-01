---
{"dg-publish":true,"permalink":"/6.AWR panopticon/pivot vba/","dgPassFrontmatter":true}
---


```
Sub CreatePivotTable()
    Dim wsData As Worksheet
    Dim wsPivot As Worksheet
    Dim rngSource As Range
    Dim pc As PivotCache
    Dim pt As PivotTable

    ' 원본 데이터가 있는 워크시트와 범위를 설정합니다.
    Set wsData = ThisWorkbook.Worksheets("Sheet1")
    Set rngSource = wsData.Range("A1").CurrentRegion

    ' 피벗 테이블이 생성될 워크시트를 설정합니다.
    Set wsPivot = ThisWorkbook.Worksheets("Sheet2")

    ' 피벗 캐시를 생성하고 피벗 테이블을 추가합니다.
    Set pc = ThisWorkbook.PivotCaches.Create(xlDatabase, rngSource)
    Set pt = pc.CreatePivotTable(wsPivot.Range("A3"), "PivotTable1")

    ' 필드를 추가하고 설정합니다.
    With pt
        ' 행 필드 추가
        .PivotFields("필드이름1").Orientation = xlRowField
        .PivotFields("필드이름1").Position = 1

        ' 열 필드 추가
        .PivotFields("필드이름2").Orientation = xlColumnField
        .PivotFields("필드이름2").Position = 1

        ' 데이터 필드 추가
        .AddDataField .PivotFields("필드이름3"), "합계", xlSum
    End With

    ' 피벗 테이블을 새로고침합니다.
    pt.RefreshTable
End Sub

```