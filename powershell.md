# Powershell

Verify hash one-liner in powershell
--

```
PS C:\DIR> (Get-FileHash -Algorithm SHA256 FILE).hash -eq "HASH"
```

💡 @see [Microsoft, 21.12.2022](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-7.3)