A schema plugin provides the BIMserver with information about a schema. There is currently only one implementation, and that one can read an express schema file (currently the Ifc2x3tc1.exp).

```java
public interface SchemaPlugin extends Plugin {
	SchemaDefinition getSchemaDefinition(PluginConfiguration pluginConfiguration);
	File getExpressSchemaFile();
}
```