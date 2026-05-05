# Enumeración con ldapsearch


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
ldapsearch (samAccountType=805306369) --attributes distinguishedName,dNSHostName,objectSid,userAccountControl
```


Dominio / OU
```
ldapsearch (|(objectClass=domain)(objectClass=organizationalUnit))
```

GPO
```
ldapsearch (objectClass=groupPolicyContainer)
```

Relaciones de Confianza
```
ldapsearch (objectClass=trustedDomain)
```


Delegación No Restringida (Unconstrained Delegation)
```
ldapsearch (&(samAccountType=805306369)(userAccountControl:1.2.840.113556.1.4.803:=524288)) --attributes samaccountname
```

Delegación Restringida (Constrained Delegation)
```
ldapsearch (&(samAccountType=805306369)(msDS-AllowedToDelegateTo=*)) --attributes samAccountName,msDS-AllowedToDelegateTo
```

Delegación restringida basada en recursos (Resource Based Constrained Delegation / RBCD)
```
ldapsearch "(&(samAccountType=805306369)(msDS-AllowedToActOnBehalfOfOtherIdentity=*))" --attributes samAccountName,msDS-AllowedToActOnBehalfOfOtherIdentity
```


Estructura y ACLs 
```
ldapsearch (|(objectClass=domain)(objectClass=organizationalUnit)(objectClass=groupPolicyContainer)) --attributes *,ntsecuritydescriptor
```
> - ⚠️ SOLO si necesitas ACLs
> - ❌ Evítalo por defecto (ruido + tiempo)


ACL de un objeto específico
```
ldapsearch (objectSid=TARGET_SID) --attributes ntsecuritydescriptor
```

GPOs y sus filtros WMI
```
ldapsearch (objectClass=groupPolicyContainer) --attributes displayName,gPCWQLFilter
```
> - `gPCWQLFilter` si vacío → GPO aplica a todos los equipos de la OU
> - `gPCWQLFilter` con GUID → GPO tiene un filtro → hay que verificar


Vinculación de filtros WMI
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
