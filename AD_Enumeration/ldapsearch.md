
Usuarios
```
ldapsearch (&(samAccountType=805306368)(!(objectClass=computer))) --attributes distinguishedName,objectSid,userAccountControl,servicePrincipalName,adminCount,description
```


Grupos
```
ldapsearch (objectClass=group) --attributes distinguishedName,objectSid,member,adminCount,description
```


Computadores
```
ldapsearch (samAccountType=805306369) --attributes distinguishedName,dNSHostName,objectSid,userAccountControl,msDS-AllowedToDelegateTo
```


Dominio / OU
```
ldapsearch (|(objectClass=domain)(objectClass=organizationalUnit))
```

GPO
```
ldapsearch (objectClass=groupPolicyContainer)
```

Trust relationship
```
ldapsearch (objectClass=trustedDomain)
```


Auditoría de delegación y ACLs (ntsecuritydescriptor)
```
ldapsearch (|(objectClass=domain)(objectClass=organizationalUnit)(objectClass=groupPolicyContainer)) --attributes *,ntsecuritydescriptor
```
> - ⚠️ SOLO si necesitas ACLs
> - ❌ Evítalo por defecto (ruido + tiempo)


Auditoría de delegación y ACLs específico (ntsecuritydescriptor) (mas Stealth)
```
ldapsearch (objectSid=TARGET_SID) --attributes ntsecuritydescriptor
```


Detectar si GPO tiene filtros WMI
```
ldapsearch (objectClass=groupPolicyContainer) --attributes displayName,gPCWQLFilter
```

> - `gPCWQLFilter` vacío → GPO aplica a todos los equipos de la OU
> - `gPCWQLFilter` con GUID → GPO tiene un filtro → hay que verificar

Consultar el filtro WMI
```
ldapsearch (objectClass=msWMI-Som) --attributes name,msWMI-Parm2
```
> - Solo si un `GUID` fue detectado en el paso anterior.
> - GUID → query WMI → ¿mi target cumple? ¿Esta GPO realmente afecta a mi objetivo?

REVISAR GptTmpl.inf

```
## REVISAR GptTmpl.inf
- Solo si GPO dice Admin / Workstation Admin / RDP / Helpdesk / IT
- Solo si afecta computadoras

## UBICACIÓN
SYSVOL → Policies → {GUID} → GptTmpl.inf
- Contiene SID del grupo con admin local

## ACCIÓN EN BLOODHOUND
MERGE (Group)-[:AdminTo]->(Computer)
```
