To make it easier to create serializer plugins for the BIMserver, you can also define the serialization logic in an XSLT which takes an IFCXML file as the source file.

# Creating the plugin

Note: Is is usually a lot easier to start from existing code, have a look at the "Cobie2Serializer" project for example.

- Follow the [Plugin Development](Plugin-development.md) page
- Add a dependancy to the "XsltSerializer" project
- Create an xslt file in the plugin-project's root
- Change the "TestPlugin" file:

```java
	private boolean initialized;
	private URL url;

	@Override
	public void init(PluginManager pluginManager) throws PluginException {
		url = pluginManager.getPluginContext(this).getResourceAsUrl("[NAME OF YOUR XSLT FILE]");
		initialized = true;
	}

	@Override
	public String getName() {
		return getClass().getName();
	}

	@Override
	public String getDescription() {
		return "Cobie2";
	}

	@Override
	public String getVersion() {
		return "1.0";
	}

	@Override
	public boolean isInitialized() {
		return initialized;
	}

	@Override
	public Set<Class<? extends Plugin>> getRequiredPlugins() {
		return null;
	}

	@Override
	public EmfSerializer createSerializer() {
		XsltSerializer xsltSerializer = new XsltSerializer();
		xsltSerializer.setXsltUrl(url);
		return xsltSerializer;
	}

	@Override
	public String getDefaultSerializerName() {
		return "[NAME OF YOUR SERIALIZER]";
	}

	@Override
	public String getDefaultExtension() {
		return "[EXTENSION OF YOU XSLT TARGET FORMAT]";
	}

	@Override
	public String getDefaultContentType() {
		return "[CONTENT TYPE OF YOUR XSLT TARGET FORMAT]";
	}
```

Example project structure:
http://bimserver.googlecode.com/svn/wiki/images/cobieproject.png

# Adding XSLT parameters

You can pass parameters to the XSLT engine the following way. You have to add this code to the init method.

```java
	xsltSerializer.addParameter(new XsltParameter("topic", "component"));
```
