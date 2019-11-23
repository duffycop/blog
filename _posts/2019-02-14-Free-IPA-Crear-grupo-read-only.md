---
title:  FreeIPA - Crear grupo Read-only
key: 20190214
author: Tino
layout: article
---

### Para que queremos un grupo de Read-only?

Bueno, en el [post anterior]({% post_url 2019-02-08-Instalando-Free-IPA %}) vimos como instalar FreeIPA y también comentamos que nos sirve para autenticar el acceso a diferentes servicios con FreeIPA, para ello vamos a crear un grupo de read-only en el cual crearemos un usuario para bindear y usar FreeIPA como autenticador de otros servicios.<!-- more -->  

### Que necesitamos?

Tener FreeIPA instalado y configurado como vimos [aquí]({% post_url 2019-02-08-Instalando-Free-IPA %}).  

### Agregar nuevo grupo de usuarios Read-only

1. Lo primero es crear el grupo, para esto como siempre nos conectamos por ssh al FreeIPA y luego ejecutamos lo siguiente.
~~~ bash
ipa group-add readonly --desc "A read-only group to bind LDAP authentication"
~~~
La salida de este comando debe ser la siguiente (o muy parecida):
```code
----------------------
Added group "readonly"
----------------------
  Group name: readonly
  Description: A read-only group to bind LDAP authentication
  GID: 836200003
```
2. Crear el rol
```
ipa role-add
```
```code
Role name: read-only
----------------------
Added role "read-only"
----------------------
  Role name: read-only
```
3. Crear privilegios
```
ipa privilege-add
```
```code
Privilege name: read-only
---------------------------
Added privilege "read-only"
---------------------------
  Privilege name: read-only
```
4. Agregar los permisos
```
ipa permission-add 'readonly' --targetgroup=readonly --permissions=read --attrs=member
```
```code
---------------------------
Added permission "readonly"
---------------------------
  Permission name: readonly
  Granted rights: read
  Effective attributes: member
  Bind rule type: permission
  Subtree: dc=ipa,dc=tino,dc=uy
  Target DN: cn=readonly,cn=groups,cn=accounts,dc=ipa,dc=tino,dc=uy
  Target group: readonly
  Permission flags: SYSTEM, V2
```
5. Asignamos
```
ipa role-add-privilege read-only
``
```code
[privilege]: read-only
  Role name: read-only
  Privileges: read-only
----------------------------
Number of privileges added 1
----------------------------
```
```
ipa privilege-add-permission
```
```code
[permission]: readonly
  Privilege name: read-only
  Permissions: readonly
  Granting privilege to roles: read-only
-----------------------------
Number of permissions added 1
-----------------------------
```
Necesitamos usuario y contrasena de FreeIPA y crear el usuario viewonly en el panel del menu.
```
ipa role-add-member
```
```code
Role name: read-only
[member user]: viewonly
[member group]:
[member host]:
[member host group]:
[member service]:
  Role name: read-only
  Member users: viewonly
  Privileges: read-only
-------------------------
Number of members added 1
-------------------------
```
```
ipa group-add-member
```
```code
Group name: readonly
[member user]: viewonly
[member group]:
  Group name: readonly
  Description: A read-only group to bind LDAP authentication
  GID: 836200003
  Member users: viewonly
-------------------------
Number of members added 1
-------------------------
```  

Listo, tenemos el grupo readonly creado y solo tiene permisos de lectura en el árbol del directorio de FreeIPA.
