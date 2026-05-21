# Enumeración de políticas y reglas de AppLocker
___

Nivel de restricción de seguridad de consola (modo de lenguaje)
```powershell
$ExecutionContext.SessionState.LanguageMode
```

Políticas desde el registro local
```powershell
Get-ChildItem 'HKLM:Software\Policies\Microsoft\Windows\SrpV2'
```
```powershell
Get-ChildItem 'HKLM:Software\Policies\Microsoft\Windows\SrpV2\Exe'
```

Cmdlet nativo de AppLocker, **Get-AppLockerPolicy**
```powershell
$policy = Get-AppLockerPolicy -Effective
$policy.RuleCollections
```
