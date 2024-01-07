---
{"dg-publish":true,"permalink":"/6.AWR panopticon/user env/","dgPassFrontmatter":true,"noteIcon":""}
---



```
'List of Environment Variables in VBA
Sub vbaf1_List_Environment_Variables()

    'Variable declaration
    Dim iCnt As Integer
    Dim sEnvVariable As String
    
    'loop through all environ values from 1 to 250
    For iCnt = 1 To 250
        ' Get the environment variable
        sEnvVariable = Environ(iCnt)
        
        'Check for the Environment variable
        If Len(sEnvVariable) > 0 Then
            Debug.Print iCnt, Environ(iCnt)
        Else
            Exit For
        End If
    Next
End Sub
```

| N0 | Environment Variable            | N0 | Environment Variable      |
|----|---------------------------------|----|---------------------------|
| 1  | ALLUSERSPROFILE                 | 23 | PROCESSOR_ARCHITEW6432    |
| 2  | APPDATA                         | 24 | PROCESSOR_IDENTIFIER      |
| 3  | CommonProgramFiles              | 25 | PROCESSOR_LEVEL           |
| 4  | CommonProgramFiles(x86)         | 26 | PROCESSOR_REVISION        |
| 5  | CommonProgramW6432              | 27 | ProgramData               |
| 6  | COMPUTERNAME                    | 28 | ProgramFiles              |
| 7  | ComSpec                         | 29 | ProgramFiles(x86)         |
| 8  | DriverData                      | 30 | ProgramW6432              |
| 9  | FPS_BROWSER_APP_PROFILE_STRING  | 31 | PSModulePath              |
| 10 | FPS_BROWSER_USER_PROFILE_STRING | 32 | PUBLIC                    |
| 11 | HOMEDRIVE                       | 33 | RegionCode                |
| 12 | HOMEPATH                        | 34 | SESSIONNAME               |
| 13 | LOCALAPPDATA                    | 35 | SystemDrive               |
| 14 | LOGONSERVER                     | 36 | SystemRoot                |
| 15 | NUMBER_OF_PROCESSORS            | 37 | TEMP                      |
| 16 | OneDrive                        | 38 | TMP                       |
| 17 | OnlineServices                  | 39 | USERDOMAIN                |
| 18 | OS                              | 40 | USERDOMAIN_ROAMINGPROFILE |
| 19 | Path                            | 41 | USERNAME                  |
| 20 | PATHEXT                         | 42 | USERPROFILE               |
| 21 | platformcode                    | 43 | windir                    |
| 22 | PROCESSOR_ARCHITECTURE          |

- cpu info & mac address 출력
```
 Option Explicit

Sub 매크로1()

    Dim objWMIService As Object
    Dim colItems As Object
    Dim objItem As Object
    Dim strComputer As String
    Dim row As Integer
    Dim i As Integer
    i = 1
    
    Range("A1").Value = "ProcessorIdentifier" 
    Range("B1").Value = Replace(Environ("PROCESSOR_IDENTIFIER"), " ", "_")
    Range("A2").Value = "Hostname"
    Range("B2").Value = Environ("COMPUTERNAME")
    Range("A3").Value = "UserName"
    Range("B3").Value = Environ("UserName")

    strComputer = "." ' 현재 컴퓨터를 사용하려면 "."을 사용
    row = 4 ' 출력을 시작할 행 번호
    
    Set objWMIService = GetObject("winmgmts:\\" & strComputer & "\root\CIMV2")
    Set colItems = objWMIService.ExecQuery("SELECT MACAddress FROM Win32_NetworkAdapterConfiguration WHERE MACAddress IS NOT NULL")
    
    For Each objItem In colItems
        Dim macAddresses As Variant
        macAddresses = objItem.MACAddress
        
        If IsArray(macAddresses) Then
            For i = LBound(macAddresses) To UBound(macAddresses)
                Range("A" & row).Value = "MAC Address:" & row - 3
                Range("B" & row).Value = macAddresses(i)
                row = row + 1
            Next i
        Else
            Range("A" & row).Value = "MAC Address:" & row - 3
            Range("B" & row).Value = macAddresses
            row = row + 1
        End If
    Next
         
End Sub


```


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