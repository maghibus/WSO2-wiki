# Wso2
## Accesso ai db  H2 

Aggiungere il seguente blocco xml al **carbon.xml**
```xml
<H2DatabaseConfiguration>
	<property name="web"/>
    <property name="webPort">8082</property>
    <property name="webAllowOthers"/>
</H2DatabaseConfiguration>
```
Connettersi al [pannello di controllo H2](http://localhost:8082/)

Copiare la configurazione da **/repository/conf/datasources/master-datasources.xml**



<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIwNjkwNDM4NjZdfQ==
-->