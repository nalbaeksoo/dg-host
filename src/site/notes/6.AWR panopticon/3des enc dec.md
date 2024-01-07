---
{"dg-publish":true,"permalink":"/6.AWR panopticon/3des enc dec/","dgPassFrontmatter":true,"noteIcon":""}
---


2023-05-15 / 17:22

- 암호화 =TripleDESEncrypt("암호화할 평문", "키")
```
Option Explicit

' 3DES 암호화 함수
Function TripleDESEncrypt(ByVal plainText As String, ByVal key As String) As String
    Dim des As Object
    Dim encryptedData As String
    
    Set des = CreateObject("System.Security.Cryptography.TripleDESCryptoServiceProvider")
    
    des.Mode = 1 ' CBC 모드
    des.Padding = 2 ' PKCS7 패딩
    
    ' 24바이트(192비트) 키 생성
    Dim keyBytes() As Byte
    keyBytes = StrConv(key, vbFromUnicode)
    ReDim Preserve keyBytes(23)
    
    des.Key = keyBytes
    
    Dim ivBytes() As Byte
    ivBytes = StrConv("01234567", vbFromUnicode) ' 초기화 벡터 (8바이트)
    des.IV = ivBytes
    
    ' 평문을 바이트 배열로 변환
    Dim plainBytes() As Byte
    plainBytes = StrConv(plainText, vbFromUnicode)
    
    ' 암호화
    Dim transform As Object
    Set transform = des.CreateEncryptor()
    
    Dim cryptoStream As Object
    Set cryptoStream = CreateObject("System.IO.MemoryStream")
    
    cryptoStream.Write(plainBytes, 0, UBound(plainBytes) + 1)
    cryptoStream.Position = 0
    
    Dim cryptoTransform As Object
    Set cryptoTransform = transform
    Dim buffer() As Byte
    ReDim buffer(cryptoStream.Length - 1)
    
    cryptoTransform.TransformBlock cryptoStream.GetBuffer(), 0, cryptoStream.Length, buffer, 0
    
    encryptedData = StrConv(buffer, vbUnicode)
    
    TripleDESEncrypt = encryptedData
End Function

```

- 복호화 =TripleDESDecrypt("복호화할 암호문", "키")
```
Option Explicit

' 3DES 복호화 함수
Function TripleDESDecrypt(ByVal encryptedText As String, ByVal key As String) As String
    Dim des As Object
    Dim decryptedData As String
    
    Set des = CreateObject("System.Security.Cryptography.TripleDESCryptoServiceProvider")
    
    des.Mode = 1 ' CBC 모드
    des.Padding = 2 ' PKCS7 패딩
    
    ' 24바이트(192비트) 키 생성
    Dim keyBytes() As Byte
    keyBytes = StrConv(key, vbFromUnicode)
    ReDim Preserve keyBytes(23)
    
    des.Key = keyBytes
    
    Dim ivBytes() As Byte
    ivBytes = StrConv("01234567", vbFromUnicode) ' 초기화 벡터 (8바이트)
    des.IV = ivBytes
    
    ' 암호문을 바이트 배열로 변환
    Dim encryptedBytes() As Byte
    encryptedBytes = StrConv(encryptedText, vbFromUnicode)
    
    ' 복호화
    Dim transform As Object
    Set transform = des.CreateDecryptor()
    
    Dim cryptoStream As Object
    Set cryptoStream = CreateObject("System.IO.MemoryStream")
    
    cryptoStream.Write(encryptedBytes, 0, UBound(encryptedBytes) + 1)
    cryptoStream.Position = 0
    
    Dim cryptoTransform As Object
    Set cryptoTransform = transform
    Dim buffer() As Byte
    ReDim buffer(cryptoStream.Length - 1)
    
    cryptoTransform.TransformBlock cryptoStream.GetBuffer(), 0, cryptoStream.Length, buffer, 0
    
    decryptedData = StrConv(buffer, vbUnicode)
    
    ' 패딩 제거
    decryptedData = Left(decryptedData, Len(decryptedData) - Asc(Right(decryptedData, 1)))
    
    TripleDESDecrypt = decryptedData
End Function

```