---
{"dg-publish":true,"permalink":"/6.AWR panopticon/user env/","dgPassFrontmatter":true}
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
