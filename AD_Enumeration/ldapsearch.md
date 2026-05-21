# Enumeración con ldapsearch


Usuarios
```powershell
ldapsearch (&(samAccountType=805306368)(!(objectClass=computer))) --attributes sAMAccountName,distinguishedName,objectSid,userAccountControl,servicePrincipalName,adminCount,description
```


Grupos
```powershell
ldapsearch (objectClass=group) --attributes sAMAccountName,distinguishedName,objectSid,member,adminCount,description
```


Computadores
```powershell
ldapsearch (samAccountType=805306369) --attributes sAMAccountName,distinguishedName,dNSHostName,objectSid,userAccountControl
```


Dominio / OU
```powershell
ldapsearch (|(objectClass=domain)(objectClass=organizationalUnit))
```

GPO
```powershell
ldapsearch (objectClass=groupPolicyContainer)
```

Relaciones de Confianza
```powershell
ldapsearch (objectClass=trustedDomain)
```


Delegación No Restringida (Unconstrained Delegation)
```powershell
ldapsearch (&(samAccountType=805306369)(userAccountControl:1.2.840.113556.1.4.803:=524288)) --attributes samaccountname
```

Delegación Restringida (Constrained Delegation)
```powershell
ldapsearch (&(samAccountType=805306369)(msDS-AllowedToDelegateTo=*)) --attributes samAccountName,msDS-AllowedToDelegateTo
```

Delegación restringida basada en recursos (Resource Based Constrained Delegation / RBCD)
```powershell
ldapsearch "(&(samAccountType=805306369)(msDS-AllowedToActOnBehalfOfOtherIdentity=*))" --attributes samAccountName,msDS-AllowedToActOnBehalfOfOtherIdentity
```


Estructura y ACLs 
```powershell
ldapsearch (|(samAccountType=805306368)(samAccountType=805306369)(samAccountType=268435456)) --attributes *,ntsecuritydescriptor
```
```powershell
ldapsearch (|(objectClass=domain)(objectClass=organizationalUnit)(objectClass=groupPolicyContainer)) --attributes *,ntsecuritydescriptor
```
> - ⚠️ SOLO si necesitas ACLs
> - ❌ Evítalo por defecto (ruido + tiempo)


ACL de un objeto específico
```powershell
ldapsearch (objectSid=TARGET_SID) --attributes ntsecuritydescriptor
```

GPOs y sus filtros WMI
```powershell
ldapsearch (objectClass=groupPolicyContainer) --attributes displayName,gPCWQLFilter
```
> - `gPCWQLFilter` si vacío → GPO aplica a todos los equipos de la OU.
> - `gPCWQLFilter` con GUID → Hay que verificar a quien aplica.


Vinculación de filtros WMI
```powershell
ldapsearch (objectClass=msWMI-Som) --attributes name,msWMI-Name,msWMI-Parm2
```
> - Condición: Solo si se detectó un GUID en gPCWQLFilter.
> - Función: Traduce el GUID a la consulta WMI real.
> - Objetivo: Confirmar si el target cumple los requisitos técnicos para aplicar la GPO.



> ## REVISAR GptTmpl.inf
> - Solo si GPO dice Admin / Workstation Admin / RDP / Helpdesk / IT
> - Solo si afecta computadoras
> - UBICACIÓN → `\\<DOMAIN>\SYSVOL\<DOMAIN>\Policies\{GUID}\Machine\Microsoft\Windows NT\SecEdit\GptTmpl.inf`

