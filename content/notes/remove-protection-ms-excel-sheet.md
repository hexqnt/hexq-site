+++
title = "Снятие защиты листа MS Excel"
date = 2020-05-27
description = "Редактирование XLSX и снятие защиты листа с помощью макроса."
slug = "remove-protection-ms-excel-sheet"
aliases = ["/2020/05/27/remove-protection-ms-excel-sheet.html"]
+++

Для снятия защиты можно воспользоваться одним из 2-х методов.

## Путём прямого редактирования файла
1. Открываем *.xlsx файл архиватором, поддерживающим zip-формат ([winrar](https://www.rarlab.com/) или [7zip](https://www.7-zip.org/))
2. Далее заходим в "..\xl\worksheets\"
3. Открываем нужный нам лист, они будут называться "sheet1.xml", "sheet2.xml" итд.
4. В файле находим тег "sheetProtection" и удаляем его полностью
5. Сохраняем файл, если появится сообщение от архиватора типа "в файл были внесены изменения, сохранить?", жмём ДА

Аналогичным образом снимается пароль и с док. Word. Только нужно искать тег: "<w:UnprotectPassword></w:UnprotectPassword>"

## Перебор пароля скриптом
Для перебора пароля нужно запустить данный макрос:

```
Sub PasswordBreaker()
  Dim i As Integer, j As Integer, k As Integer
  Dim l As Integer, m As Integer, n As Integer
  Dim i1 As Integer, i2 As Integer, i3 As Integer
  Dim i4 As Integer, i5 As Integer, i6 As Integer
  On Error Resume Next
  For i = 65 To 66: For j = 65 To 66: For k = 65 To 66
  For l = 65 To 66: For m = 65 To 66: For i1 = 65 To 66
  For i2 = 65 To 66: For i3 = 65 To 66: For i4 = 65 To 66
  For i5 = 65 To 66: For i6 = 65 To 66: For n = 32 To 126
  ActiveSheet.Unprotect Chr(i) & Chr(j) & Chr(k) & _
      Chr(l) & Chr(m) & Chr(i1) & Chr(i2) & Chr(i3) & _
      Chr(i4) & Chr(i5) & Chr(i6) & Chr(n)
  If ActiveSheet.ProtectContents = False Then
    Dim strSample As String
    strSample = Chr(i) & Chr(j) & _
      Chr(k) & Chr(l) & Chr(m) & Chr(i1) & Chr(i2) & _
      Chr(i3) & Chr(i4) & Chr(i5) & Chr(i6) & Chr(n)
    MsgBox "Пароль: " & strSample & " скопирован в буфер обмена"
    Dim clipboard As MSForms.DataObject
    Set clipboard = New MSForms.DataObject
    clipboard.SetText strSample
    clipboard.PutInClipboard
    Exit Sub
  End If
  Next: Next: Next: Next: Next: Next
  Next: Next: Next: Next: Next: Next
End Sub
```
