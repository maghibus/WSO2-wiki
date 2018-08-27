# Wso2
## API REST
Esempio di un API con parametri **formData**
```yml
swagger: "2.0"
paths:
  /users:
    post:
      parameters:
        - name: name
          in: formData
          required: true
          type: string
        - name: job
          in: formData
          required: true
          type: string
      responses:
        "200":
          description: ""
      x-auth-type: "Application & Application User"
      x-throttling-tier: Unlimited
      consumes:
        - application/x-www-form-urlencoded
      produces:
        - application/json
```
 Per processare correttamente il messaggio occorre impostare:
```yml
consumes:
	- application/x-www-form-urlencoded
```
Nella in-sequence è possibile recuperare i parametri con XPath. Il nodo radice dei parametri è **xformValues**
```xml
<property expression="//xformValues/name/text()" name="formname" scope="default" type="STRING"/>
<property expression="//xformValues/job/text()" name="formjob" scope="default" type="STRING"/>
```

## Configuring JWT
Configurare JWT in 
`<APIM_HOME>/repository/conf/api-manager.xml` 
```xml
<JWTConfiguration>
	<EnableJWTGeneration>true</EnableJWTGeneration>
	<JWTHeader>X-JWT-Assertion</JWTHeader>	
	<ClaimsRetrieverImplClass>org.wso2.carbon.apimgt.impl.token.DefaultClaimsRetriever</ClaimsRetrieverImplClass>       
	<SignatureAlgorithm>SHA256withRSA</SignatureAlgorithm>
	<JWTGeneratorImpl>org.wso2.carbon.apimgt.keymgt.token.JWTGenerator</JWTGeneratorImpl>
</JWTConfiguration>
``` 

### Esempio Token JWT
Nel token JWT sono presenti i ruoli del'utente
```json
{
"typ":"JWT",
"alg":"RS256",
"x5t":"a_jhNus21KVuoFx65LmkW2O_l10"
}
{
"http://wso2.org./claims/role":[
"Internal/subscriber",
"Application/admin_zcxxzc_PRODUCTION",
"Internal/creator"``,
"Application/admin_TestApplication1_PRODUCTION",
"Application/admin_JWTTestApplication_PRODUCTION",
"Internal/publisher",
"Internal/everyone",
"admin"
], 
	...
}
```

## IN-Sequence
Nella in-sequence andiamo a leggere i ruoli dal token JWT.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<sequence name="fetch_user_role" trace="disable" xmlns="http://ws.apache.org/ns/synapse">
    <log level="full"/>
    <property expression="//xformValues/name/text()" name="formname" scope="default" type="STRING"/>
    <property expression="//xformValues/job/text()" name="formjob" scope="default" type="STRING"/>
    <property expression="get-property('transport','X-JWT-Assertion')" name="jwt-header" scope="default" type="STRING"/>
    <script language="js">
    <![CDATA[
	    var jwt = mc.getProperty('jwt-header').trim();
		var jwtPayload = jwt.split("\\.")[1];
		var jsonStr = Packages.java.lang.String(Packages.org.apache.commons.codec.binary.Base64.decodeBase64(jwtPayload));

		var jwtJson = JSON.parse(jsonStr);

		var roles = jwtJson['http://wso2.org/claims/role'];
		var crm = roles.indexOf("crm") != -1;
		mc.setProperty("roles",JSON.stringify(roles));
		mc.setProperty("crm",crm);]]></script>
    <log level="custom">
        <property expression="$ctx:formname" name="form Name"/>
        <property expression="$ctx:formjob" name="form Job"/>
        <property expression="$ctx:roles" name="Roles"/>
        <property expression="$ctx:crm" name="crm"/>
    </log>
    <filter regex="true" source="$ctx:crm">
        <then>
            <property expression="$ctx:formname" name="UserName" scope="default" type="STRING"/>
        </then>
        <else>
            <property expression="$ctx:api.ut.userName" name="UserName" scope="default" type="STRING"/>
        </else>
    </filter>
    <log level="custom">
        <property expression="$ctx:UserName" name="UserName"/>
    </log>
    <payloadFactory media-type="json">
        <format>{"name" : "$1", "job" : "$2"}</format>
        <args>
            <arg evaluator="xml" expression="get-property('UserName')"/>
            <arg evaluator="xml" expression="get-property('formjob')"/>
        </args>
    </payloadFactory>
</sequence>

``` 

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTExMDE3NzMyNjVdfQ==
-->