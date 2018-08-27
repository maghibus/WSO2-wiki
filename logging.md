# Wso2
## Abilitare il log su API
Per stampare sul log in DEBUG le tracce delle chiamate HTTP editare **/conf/log4j.properties** e togliere il commetto alle seguenti direttive:

```INI
#log4j.logger.org.apache.synapse.transport.http.headers=DEBUG
#log4j.logger.org.apache.synapse.transport.http.wire=DEBUG
```

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE5NDQ1NjE2ODJdfQ==
-->