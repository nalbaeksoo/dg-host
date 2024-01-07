---
{"dg-publish":true,"permalink":"/6.AWR panopticon/각 table의 min,max값/","dgPassFrontmatter":true,"noteIcon":""}
---


각 시계열 데이터가 빈 행으로 구분되어 있고, 각각의 시작점이 A4, E4, I4이며, 길이가 다르다는 것을 고려하여 VBA 코드를 작성했습니다. 코드는 각 데이터 열의 최솟값, 최댓값, 평균값을 계산한 후 B1, B2, B3 등의 위치에 결과값을 출력합니다.

vbaCopy code

```
Sub CalculateMinMaxAvg()
    Dim ws As Worksheet
    Set ws = ThisWorkbook.ActiveSheet
    
    ' 각 데이터의 시작 위치를 배열에 저장
    Dim StartCells As Variant
    StartCells = Array("A4", "E4", "I4")
    
    Dim i As Long
    Dim LastRow As Long
    Dim DataRange As Range
    Dim minVal As Double, maxVal As Double, avgVal As Double
    
    ' 각 데이터에 대해 최솟값, 최댓값, 평균값 계산
    For i = LBound(StartCells) To UBound(StartCells)
        ' 각 데이터 열의 마지막 행 찾기
        LastRow = ws.Cells(ws.Rows.Count, Range(StartCells(i)).Column).End(xlUp).Row
        
        ' 데이터 범위 설정
        Set DataRange = ws.Range(StartCells(i) & ":" & ws.Cells(LastRow, Range(StartCells(i)).Column + 2))
        
        ' 최솟값, 최댓값, 평균값 계산
        minVal = Application.WorksheetFunction.Min(DataRange)
        maxVal = Application.WorksheetFunction.Max(DataRange)
        avgVal = Application.WorksheetFunction.Average(DataRange)
        
        ' 결과 출력
        ws.Cells(1, Range(StartCells(i)).Column + 1).Value = minVal
        ws.Cells(2, Range(StartCells(i)).Column + 1).Value = maxVal
        ws.Cells(3, Range(StartCells(i)).Column + 1).Value = avgVal
    Next i

End Sub

```

이 코드를 실행하면, 각 데이터 열에 대한 최솟값, 최댓값, 평균값이 B1, B2, B3과 같은 위치에 출력됩니다.