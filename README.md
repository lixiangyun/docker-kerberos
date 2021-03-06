# docker-kerberos

This project is an example MIT Kerberos v5 containerisation.

It is intended for demonstration / learning on a local host and is not production ready. In particular weak passwords are used.

# Run
```
docker volume create krb5kdc
docker volume create database

docker run --restart=always --name kadmin -p 749:749 -p 464:464 -d \
        -v $PWD/krb5.conf:/etc/kadmin/krb5.conf \
        -v /var/log/kerberos:/var/log/kerberos \
        -v krb5kdc:/etc/krb5kdc \
        -v database:/var/lib/krb5kdc \
        linimbus/kerberos-kadmin

docker run --restart=always --name kdc -p 88:88 -d \
        -v $PWD/krb5.conf:/etc/kdc/krb5.conf \
        -v /var/log/kerberos:/var/log/kerberos \
        -v krb5kdc:/etc/krb5kdc \
        -v database:/var/lib/krb5kdc \
        linimbus/kerberos-kdc

```

# Create Admin
```
alias kadmin.example.org="docker exec -ti kadmin kadmin.local"
kadmin.example.org -q "add_principal admin@EXAMPLE.ORG"
kadmin.example.org -q "add_principal admin/admin@EXAMPLE.ORG"
kadmin.example.org -q "add_principal root/admin@EXAMPLE.ORG"
```
N.B. kadm5.acl is configured so that all principals ending /admin have admin rights

# Test
## Login By Password, if not found please install.(`apt install -y krb5-user && cp krb5.conf /etc/krb5.conf`)
`kadmin` or `kinit`

## Login By Keytab
`kinit -kt ./client.keytab client/admin`

## User Manager
```
kadmin addprinc client/admin
kadmin addprinc server/admin

kadmin delprinc client/admin
kadmin delprinc server/admin

kadmin listprincs
```

## Make KeyTab
```
kadmin xst -k client.keytab client/admin
kadmin xst -k server.keytab server/admin

kinit -kt ./client.keytab client/admin
kinit -kt ./server.keytab server/admin
```
