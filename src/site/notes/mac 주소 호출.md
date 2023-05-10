---
{"dg-publish":true,"permalink":"/mac 주소 호출/","dgPassFrontmatter":true}
---


```
Option Explicit

Private Type IP_ADAPTER_INFO
    NextPointer As Long
    ComboIndex As Long
    AdapterName(0 To MAX_ADAPTER_NAME_LENGTH - 1) As Byte
    Description(0 To MAX_ADAPTER_DESCRIPTION_LENGTH - 1) As Byte
    AddressLength As Integer
    Address(0 To MAX_ADAPTER_ADDRESS_LENGTH - 1) As Byte
    Index As Long
    Type As Long
    DhcpEnabled As Long
    CurrentIpAddress As Long
    IpAddressList As IP_ADDR_STRING
    GatewayList As IP_ADDR_STRING
    DhcpServer As IP_ADDR_STRING
    HaveWins As Long
    PrimaryWinsServer As IP_ADDR_STRING
    SecondaryWinsServer As IP_ADDR_STRING
    LeaseObtained As Long
    LeaseExpires As Long
End Type

Private Type IP_ADDR_STRING
    NextPointer As Long
    IpAddress As String
    IpMask As String
    Context As Long
End Type

Private Declare Function GetAdaptersInfo Lib "IPHLPAPI.DLL" (ByVal lpAdapterInfo As Long, ByRef lpcbBuffer As Long) As Long
Private Declare Sub CopyMemory Lib "kernel32" Alias "RtlMoveMemory" (ByRef Destination As Any, ByRef Source As Any, ByVal Length As Long)

Private Const MAX_ADAPTER_NAME_LENGTH As Long = 256
Private Const MAX_ADAPTER_DESCRIPTION_LENGTH As Long = 128
Private Const MAX_ADAPTER_ADDRESS_LENGTH As Long = 8

Public Function GetMacAddress() As String
    ' GetAdaptersInfo 함수를 사용하여 컴퓨터의 Mac 주소를 가져온다.
    
    ' 변수 선언
    Dim lRet As Long ' API 함수의 결과값을 저장할 Long 형 변수
    Dim abytInfo() As Byte ' Adapter 정보를 저장할 Byte 형 배열
    Dim udtInfo As IP_ADAPTER_INFO ' IP_ADAPTER_INFO 데이터 형식을 저장할 변수
    Dim lngSize As Long ' Adapter 정보의 크기를 저장할 Long 형 변수
    Dim i As Integer ' 반복문에서 사용될 Integer 형 변수
    
    ' Adapter 정보 크기 계산
    lRet = GetAdaptersInfo(0, lngSize)
    
    ' Adapter 정보 가져오기
    ReDim abytInfo(lngSize - 1) As Byte
    lRet = GetAdaptersInfo(VarPtr(abytInfo(0)), lngSize)
    
    ' Adapter 정보 파싱
    i = 0
    CopyMemory udtInfo, abytInfo(i), Len(udtInfo)
    Do While udtInfo.AddressLength = 0 And udtInfo.NextPointer <> 0
        i = i + udtInfo.NextPointer
        CopyMemory udtInfo, abytInfo(i), Len(udtInfo)
    Loop
    
    ' Mac 주소 반환
    GetMacAddress = Format$(udtInfo.Address(0), "02X") & _
                    Format$(udtInfo.Address(1), "02X") & _
                    Format$(udtInfo.Address(2), "02X") & _
                    Format$(udtInfo.Address(3), "02X") & _
                    Format$(udtInfo.Address(4), "02X") & _
                    Format$(udtInfo.Address(5), "02X")
End Function


```