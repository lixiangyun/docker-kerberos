# docker-kerberos

This project is an example MIT Kerberos v5 containerisation.

It is intended for demonstration / learning on a local host and is not production ready. In particular weak passwords are used.

# Run
```
docker volume create krb5kdc
docker run -d -v $PWD/krb5.conf:/etc/kadmin/krb5.conf -v krb5kdc:/etc/krb5kdc --net=host linimbus/kerberos-kadmin
docker run -d -v $PWD/krb5.conf:/etc/kdc/krb5.conf -v krb5kdc:/etc/krb5kdc --net=host linimbus/kerberos-kdc
```

# Administer
```
alias kadmin.example.org="docker exec -ti kadmin kadmin.local"
kadmin.example.org -q "add_principal mans0954@EXAMPLE.ORG"
kadmin.example.org -q "add_principal mans0954/admin@EXAMPLE.ORG"
```
N.B. kadm5.acl is configured so that all principals ending /admin have admin rights

