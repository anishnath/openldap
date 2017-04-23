1. : Enable ppolicy overlay
```
	ldapadd -Y EXTERNAL -H ldapi:///  -f /etc/ldap/schema/ppolicy.ldif
```

2. Create a Policies  myoupolicy.ldif OrganizationalUnit
```
	dn: ou=Policies,dc=zariga,dc=com
	objectClass: top
	objectClass: organizationalUnit
	ou: Policies
	description: My Organization policies come here
```

3. Add myoupolicy.ldif  OU

```
	ldapadd -D cn=admin,dc=zariga,dc=com -w password -f myoupolicy.ldif
```

4. Create the file ppmodule.ldif and load the pp module 

```
	dn: cn=module{0},cn=config
	changetype: modify
	add: olcModuleLoad
	olcModuleLoad: ppolicy
```

5. load the module ppmodule.ldif

```
	ldapadd -Y EXTERNAL -H ldapi:///  -f ppmodule.ldif
```

6. Prepare for Overlay Create file ppolicyoverlay.ldif

```
	dn: olcOverlay={0}ppolicy,olcDatabase={1}hdb,cn=config
	objectClass: olcOverlayConfig
	objectClass: olcPPolicyConfig
	olcOverlay: {0}ppolicy
	olcPPolicyDefault: cn=MyOrgPPolicy,ou=Policies,dc=zariga,dc=com
```

7.  Add ppolicyoverlay.ldif using ldapadd command
```
	ldapadd -Y EXTERNAL -H ldapi:/// -f ppolicyoverlay.ldif
```

8. Create passwordpolicy.ldif for MyOrganization

```
	dn: cn=MyOrgPPolicy,ou=Policies,dc=zariga,dc=com
	cn: MyOrgPPolicy
	objectClass: pwdPolicy
	objectClass: device
	objectClass: top
	pwdAttribute: userPassword
	pwdMaxAge: 3024000
	pwdExpireWarning: 1814400
	pwdInHistory: 4
	pwdCheckQuality: 1
	pwdMinLength: 9
	pwdMaxFailure: 4
	pwdLockout: TRUE
	pwdLockoutDuration: 600
	pwdGraceAuthNLimit: 0
	pwdFailureCountInterval: 0
	pwdMustChange: TRUE
	pwdAllowUserChange: TRUE
	pwdSafeModify: FALSE
```

9. Add  passwordpolicy.ldif  in LDAP

```
	ldapadd -D cn=admin,dc=zariga,dc=com -w password -f passwordpolicy.ldif
```

10. Creata a Users users.ldif

```
	dn: ou=Users,dc=zariga,dc=com
	description: My Organization Users come here
	objectclass: top
	objectclass: organizationalUnit
	ou: Users

	dn: uid=zariga,ou=Users,dc=zariga,dc=com
	cn: zariga tongy
	sn: tongy
	uid: zariga
	ou: Users
	objectClass: organizationalPerson
	objectClass: inetOrgPerson
```

11. Add this user in ldap and generate the passwoed

```
	ldapadd -D cn=admin,dc=zariga,dc=com -w password -f user.ldif 
	ldappasswd  -D cn=admin,dc=zariga,dc=com -w password 'uid=zariga,ou=users,dc=zariga,dc=com'
```

12.  Verify the configuration

```
ldapsearch -Y EXTERNAL  -H ldapi:/// -b olcDatabase={1}hdb,cn=config
```
