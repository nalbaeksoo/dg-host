---
{"dg-publish":true,"permalink":"/6.AWR panopticon/기준컬럼으로 여러개의 chart 생성/","dgPassFrontmatter":true,"noteIcon":""}
---


VBA를 이용하여 하나의 날짜(date) 컬럼으로 여러개의 데이터 컬럼을 활용하여 여러 개의 차트를 만들어보겠습니다.

예를 들어, A 열에는 날짜(date) 정보가 있고, B열부터 F열까지는 해당 날짜에 대한 데이터 정보가 있는 경우를 가정합니다. 이러한 데이터를 이용하여 5개의 차트를 만들어보겠습니다.

1.  먼저, VBA 에디터를 열어 새로운 모듈(Module)을 추가합니다.
2.  다음과 같은 VBA 코드를 입력합니다.

vbnetCopy code

```
Sub CreateCharts()

    Dim dateRange As Range
    Dim dataRange As Range
    Dim chartRange As Range
    Dim chartTitle As String
    Dim chart As ChartObject
    
    '날짜(date) 범위와 데이터 범위 지정
    Set dateRange = Range("A2:A11")
    Set dataRange = Range("B2:F11")
    
    '데이터 범위에서 각 열(데이터)마다 차트 생성
    For Each column In dataRange.Columns
    
        '차트 범위 지정
        Set chartRange = Union(dateRange, column)
        
        '차트 제목 지정
        chartTitle = column.Cells(1, 1).Value
        
        '차트 생성
        Set chart = ActiveSheet.ChartObjects.Add(Left:=300, Width:=400, Top:=25, Height:=250)
        chart.Chart.SetSourceData chartRange
        chart.Chart.ChartType = xlLineMarkers
        chart.Chart.HasTitle = True
        chart.Chart.ChartTitle.Text = chartTitle
        
    Next column
    
End Sub

```

3.  이제, 위 코드를 실행시키면 A열의 날짜(date) 정보와 B~F열의 데이터 정보를 이용하여 5개의 차트를 생성합니다. 각 차트는 해당 데이터 정보의 제목으로 생성되며, 선 형태의 차트(Line chart)로 생성됩니다.

위 코드에서는 각 열(데이터)마다 차트를 생성하고, 차트 범위를 지정할 때 `Union` 함수를 사용하여 날짜(date) 범위와 해당 열(데이터)의 범위를 합쳐서 차트 범위를 지정합니다. 이후, `ChartObjects.Add` 함수를 사용하여 차트를 생성하고, `Chart.SetSourceData` 함수를 사용하여 차트 범위를 지정하고, `ChartType` 속성을 사용하여 차트 유형을 지정합니다. 마지막으로, `ChartTitle.Text` 속성을 사용하여 차트 제목을 지정합니다.

위 코드에서는 데이터 범위를 `B2:F11`로 가정하였지만, 실제 데이터의 범위에 따라 적절하게 수정하여 사용하면 됩니다.