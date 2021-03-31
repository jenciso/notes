# OpenLDAP

Use `perl -p00e 's/\r?\n //g'` to have a friendly output

```
ldapsearch -b "ou=ag-BDC8DE1582A8FE05, ou=AppliancesContainer, ou=Partition, ou=PartitionsContainer, ou=VCDN_ROOT, ou=accessManagerContainer, o=novell" -s sub -D "cn=admin,o=novell" -x -w novell -s base romaAssociation -LLL | perl -p00e 's/\r?\n //g'
```

https://community.microfocus.com/t5/Identity-Manager-Tips/Output-of-Large-Attribute-Values-of-LDAP-Search-in-Single-Line/ta-p/1772921#:~:text=The%20output%20from%20ldapsearch%20is,line%20starting%20with%20a%20space.


