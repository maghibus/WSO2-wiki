# Retrieve user data
## API REST
Esempio di un API con parametri **formData** ed endpoint [https://reqres.in/api/users](https://reqres.in/api/)
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

### JWT
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
		"Internal/creator",
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
Nella in-sequence andiamo a leggere i ruoli dal payload JWT.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<sequence name="fetch_user_role" trace="disable" xmlns="http://ws.apache.org/ns/synapse">
    <log level="full"/>
    <property expression="//xformValues/name/text()" name="formname" scope="default" type="STRING"/>
    <property expression="//xformValues/job/text()" name="formjob" scope="default" type="STRING"/>
    <property expression="$trp:X-JWT-Assertion" name="jwt-header" scope="default" type="STRING"/>
    <script language="js">
	    <![CDATA[
		    var jwt = mc.getProperty('jwt-header').trim();
			var jwtPayload = jwt.split("\\.")[1];
			var jsonStr = Packages.java.lang.String(Packages.org.apache.commons.codec.binary.Base64.decodeBase64(jwtPayload));
			var jwtJson = JSON.parse(jsonStr);
			var roles = jwtJson['http://wso2.org/claims/role'];
			var crm = roles.indexOf("crm") != -1;
			mc.setProperty("roles",JSON.stringify(roles));
			mc.setProperty("crm",crm);
		]]>
	</script>
    <filter regex="true" source="$ctx:crm">
        <then>
            <property expression="$ctx:formname" name="userName" scope="default" type="STRING"/>
        </then>
        <else>
            <property expression="$ctx:api.ut.userName" name="userName" scope="default" type="STRING"/>
        </else>
    </filter>
    <payloadFactory media-type="json">
        <format>{"name" : "$1", "job" : "$2"}</format>
        <args>
            <arg evaluator="xml" expression="$ctx:userName"/>
            <arg evaluator="xml" expression="$ctx:formjob"/>
        </args>
    </payloadFactory>
</sequence>
``` 

## Upload IN-Sequence
See [Adding Mediation Extensions](https://docs.wso2.com/display/AM210/Adding+Mediation+Extensions)
<!--stackedit_data:
eyJwcm9wZXJ0aWVzIjoidGl0bGU6IFdTT0FNIHJldHJpZXZlIH
VzZXJpbmZvXG4iLCJoaXN0b3J5IjpbLTEwMTYxODA5NDEsLTIw
ODk4MTU2NTVdfQ==
-->