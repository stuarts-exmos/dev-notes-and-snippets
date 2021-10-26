Force a command, to avoid it asking [Y], [N] etc in scripts

Either:
```powershell
-confirm:$false
```

or:
```powershell
-confirm $false
```

or sometimes:
```powershell
-force
```
