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




<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1OTE3MDc4NjRdfQ==
-->