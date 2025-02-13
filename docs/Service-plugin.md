Service plugins can extend the functionality of a BIMserver by listening to notifications and acting upon them. For example a ClashDetection Service plugin could create a ClashDetection report as [ExtendedData] when a user checks in a new revision.

Have a look [here](Writing-a-service-the-easy-way.md) for an easier implementation of services.

# Details

For this plugin you do not need implement any specific methods as long as you subclass [ServicePlugin]. You can register your services by calling the [register] method:

```java
public void register(ServerDescriptor serverDescriptor, ServiceDescriptor serviceDescriptor, NotificationInterface notificationInterface) {
}
```

It's best to register your services in the init method. You have to provide a [ServerDescriptor] and [ServiceDescriptor], here are examples for those:

```java
ServerDescriptor serverDescriptor = StoreFactory.eINSTANCE.createServerDescriptor();
serverDescriptor.setTitle("Clashdetection");

ServiceDescriptor clashDetection = StoreFactory.eINSTANCE.createServiceDescriptor();
clashDetection.setName("Clashdetection");
clashDetection.setDescription("Clashdetection");
		clashDetection.setNotificationProtocol(AccessMethod.INTERNAL);
clashDetection.setReadRevision(true);
clashDetection.setWriteExtendedData(true);
clashDetection.setTrigger(Trigger.NEW_REVISION);
```

Example implementation code:

```java
register(serverDescriptor, clashDetection, new NotificationInterfaceAdapter(){
	@Override
	public void newLogAction(SLogAction newRevisionNotification, SToken token, String apiUrl) throws UserException, ServerException {
		ServiceInterface serviceInterface = getServiceInterface(token);
		// Here goes your code
	}
}
```

Example:

```java
public class DemoServicePlugin1 extends ServicePlugin {

	private boolean initialized;

	@Override
	public void init(PluginManager pluginManager) throws PluginException {
		super.init(pluginManager);
		initialized = true;
	}

	@Override
	public String getDescription() {
		return "Demo Service 1";
	}

	@Override
	public String getDefaultName() {
		return "Demo Service 1";
	}

	@Override
	public String getVersion() {
		return "1.0";
	}

	@Override
	public ObjectDefinition getSettingsDefinition() {
		return null;
	}

	@Override
	public boolean isInitialized() {
		return initialized;
	}

	@Override
	public String getTitle() {
		return "Demo Service 1";
	}

	@Override
	public void register(PluginConfiguration pluginConfiguration) {
		ServiceDescriptor serviceDescriptor = StoreFactory.eINSTANCE.createServiceDescriptor();
		serviceDescriptor.setProviderName("BIMserver");
		serviceDescriptor.setIdentifier(getClass().getName());
		serviceDescriptor.setName("Demo Service 1");
		serviceDescriptor.setDescription("Demo Service 1");
		serviceDescriptor.setNotificationProtocol(AccessMethod.INTERNAL);
		serviceDescriptor.setTrigger(Trigger.NEW_REVISION);
		registerNewRevisionHandler(serviceDescriptor, new NewRevisionHandler() {
			@Override
			public void newRevision(BimServerClientInterface bimServerClientInterface, long poid, long roid, long soid, SObjectType settings) throws ServerException, UserException {
				try {
					Date startDate = new Date();
					Long topicId = bimServerClientInterface.getRegistry().registerProgressOnRevisionTopic(SProgressTopicType.RUNNING_SERVICE, poid, roid, "Running Demo Service");
					for (int i=0; i<100; i++) {
						SLongActionState state = new SLongActionState();
						state.setProgress(i);
						state.setTitle("Demo Service 1");
						state.setState(SActionState.STARTED);
						state.setStart(startDate);
						bimServerClientInterface.getRegistry().updateProgressTopic(topicId, state);
						try {
							Thread.sleep(200);
						} catch (InterruptedException e) {
							e.printStackTrace();
						}
					}
					SLongActionState state = new SLongActionState();
					state.setProgress(100);
					state.setTitle("Demo Service 1");
					state.setState(SActionState.FINISHED);
					state.setStart(startDate);
					state.setEnd(new Date());
					bimServerClientInterface.getRegistry().updateProgressTopic(topicId, state);

					bimServerClientInterface.getRegistry().unregisterProgressTopic(topicId);
				} catch (PublicInterfaceNotFoundException e1) {
					e1.printStackTrace();
				}
			}
		});
	}
}
```
