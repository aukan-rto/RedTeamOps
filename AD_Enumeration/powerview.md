
- https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon
- https://powersploit.readthedocs.io/en/latest/Recon/

# Enumeración con PowerView


Importar modulo `PowerView`:
```powershell
Set-ExecutionPolicy Bypass -Scope Process
```
```powershell
import-module .\PowerView.ps1
```

Usuarios:
```powershell
Get-DomainUser
```

Usuario Específico:
```powershell
Get-DomainUser -Identity <USER> -Domain inlanefreight.local | Select-Object -Property name,samaccountname,description,memberof,whencreated,pwdlastset,lastlogontimestamp,accountexpires,admincount,userprincipalname,serviceprincipalname,useraccountcontrol
```


Saber la IP de un host:
```powershell
Resolve-DnsName -Name <nombre_de_host>
```

Usuarios con SPN configurado:
```powershell
Get-DomainUser -SPN -Properties samaccountname,ServicePrincipalName
```


Comprobar configuración PASSWD_NOTREQ:
```powershell
Get-DomainUser -UACFilter PASSWD_NOTREQD | Select-Object samaccountname,useraccountcontrol
```


Buscar contraseñas en el campo de descripción:
```powershell
Get-DomainUser * | Select-Object samaccountname,description |Where-Object {$_.Description -ne $null}
```


Miembros de Grupo (Recursivo):
```powershell
Get-DomainGroupMember -Identity "Domain Admins" -Recurse
```


Remote Desktop User Group (RDP):
```powershell
Get-NetLocalGroupMember -ComputerName ACADEMY-EA-MS01 -GroupName "Remote Desktop Users"
```

Remote Management Users Group (WinRM):
```powershell
Get-NetLocalGroupMember -ComputerName ACADEMY-EA-MS01 -GroupName "Remote Management Users"
```

Enumerar GPO :
```powershell
Get-DomainGPO | select displayname
```


Relación de confianza:
```powershell
Get-DomainTrustMapping
```

Grupos de usuarios que no pertenecen al dominio:
```powershell
Get-DomainForeignGroupMember -Domain FREIGHTLOGISTICS.LOCAL
```
```powershell
Convert-SidToName <SID>
```


Prueba de acceso Administrador Local:
```powershell
Test-AdminAccess -ComputerName ACADEMY-EA-MS01
```


Cambio contraseña:
```powershell
Set-DomainUserPassword -Identity blake -AccountPassword (ConvertTo-SecureString 'Password123!' -AsPlainText -Force ) -Verbose
```




| Función                          | Descripción                                                                                     |
| :------------------------------- | :---------------------------------------------------------------------------------------------- |
| `Export-PowerViewCSV`            | Agregar resultados a un archivo CSV                                                             |
| `ConvertTo-SID`                  | Convertir un nombre de usuario o grupo a su valor SID                                           |
| `Get-DomainSPNTicket`            | Solicita el ticket de Kerberos para una cuenta de nombre principal de servicio (SPN) especificada |

| **Funciones de dominio/LDAP:**    |                                                                                                 |
| :------------------------------- | :---------------------------------------------------------------------------------------------- |
| `Get-Domain`                     | Devolverá el objeto AD para el dominio actual (o especificado)                                  |
| `Get-DomainController`           | Devuelve una lista de los controladores de dominio para el dominio especificado                 |
| `Get-DomainUser`                 | Devolverá todos los usuarios u objetos de usuario específicos en AD                             |
| `Get-DomainComputer`             | Devolverá todas las computadoras u objetos de computadora específicos en AD                     |
| `Get-DomainGroup`                | Devolverá todos los grupos u objetos de grupo específicos en AD                                 |
| `Get-DomainOU`                   | Busque todos o objetos OU específicos en AD                                                     |
| `Find-InterestingDomainAcl`      | Encuentra ACL de objetos en el dominio con derechos de modificación establecidos                |
| `Get-DomainGroupMember`          | Devolverá los miembros de un grupo de dominio específico.                                       |
| `Get-DomainFileServer`           | Devuelve una lista de servidores que probablemente funcionen como servidores de archivos.       |
| `Get-DomainDFSShare`             | Devuelve una lista de todos los sistemas de archivos distribuidos para el dominio actual        |

| **Funciones de GPO:**            |                                                                                                 |
| :------------------------------- | :---------------------------------------------------------------------------------------------- |
| `Get-DomainGPO`                  | Devolverá todos los GPO u objetos GPO específicos en AD                                         |
| `Get-DomainPolicy`               | Devuelve la política de dominio predeterminada o la política de controlador de dominio          |

| **Funciones de enumeración por computadora:** |                                                                                          |
| :------------------------------- | :---------------------------------------------------------------------------------------------- |
| `Get-NetLocalGroup`              | Enumera grupos locales en la máquina local o remota                                             |
| `Get-NetLocalGroupMember`        | Enumera los miembros de un grupo local específico.                                              |
| `Get-NetShare`                   | Devuelve recursos compartidos abiertos en la máquina local (o remota)                           |
| `Get-NetSession`                 | Devolverá información de sesión para la máquina local (o remota)                                |
| `Test-AdminAccess`               | Prueba si el usuario actual tiene acceso administrativo a la máquina local                      |

| **Funciones 'Meta' roscadas:**    |                                                                                                 |
| :------------------------------- | :---------------------------------------------------------------------------------------------- |
| `Find-DomainUserLocation`        | Encuentra máquinas donde usuarios específicos han iniciado sesión                               |
| `Find-DomainShare`               | Encuentra recursos compartidos accesibles en máquinas de dominio                                |
| `Find-InterestingDomainShareFile`| Busca archivos que coincidan con criterios específicos en recursos compartidos                  |
| `Find-LocalAdminAccess`          | Encuentre máquinas en el dominio local donde el usuario tiene acceso de administrador local     |

| **Funciones de confianza del dominio:** |                                                                                          |
| :------------------------------- | :---------------------------------------------------------------------------------------------- |
| `Get-DomainTrust`                | Devuelve confianzas de dominio para el dominio actual o un dominio específico                   |
| `Get-ForestTrust`                | Devuelve todos los fideicomisos de bosque para el bosque actual o un bosque especificado       |
| `Get-DomainForeignUser`          | Enumera los usuarios que están en grupos fuera del dominio del usuario.                         |
| `Get-DomainForeignGroupMember`   | Enumera grupos con usuarios fuera del dominio del grupo y devuelve cada miembro extranjero      |
| `Get-DomainTrustMapping`         | Enumerará todas las confianzas para el dominio actual y cualquier otro visto.                  |
