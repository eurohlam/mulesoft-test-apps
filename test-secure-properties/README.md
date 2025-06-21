# Test secured configuration properties

https://docs.mulesoft.com/mule-runtime/latest/secure-configuration-properties
	
The project contains secured properties `app.properties` where `database.password` is encrypted

The following command has been used for the encryption:

	java -cp secure-properties-tool-j17.jar com.mulesoft.tools.SecurePropertiesTool \
	string \
	encrypt \
	Blowfish \
	CBC \
	encryptKey \
	"databasePassword"
