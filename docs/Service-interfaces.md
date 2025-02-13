The `Service Interfaces` are a set of defined interfaces for interaction with BIMserver. These interfaces are defined as (heavily annotated) Java interfaces.

In BIMserver 1.3/1.4 a lot of methods were moved to interfaces that are prefixed with `org.buildingsmart.bimsie1`. In BIMserver 1.5 those methods have been moved back to the original interfaces.

# The interfaces

| Namespace     | Name              | Link                                                                                                                                                   |
| ------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| org.bimserver | AdminInterface    | [AdminInterface.java](https://github.com/opensourceBIM/BIMserver/blob/master/PluginBase/src/org/bimserver/shared/interfaces/AdminInterface.java)       |
| org.bimserver | AuthInterface     | [AuthInterface.java](https://github.com/opensourceBIM/BIMserver/blob/master/PluginBase/src/org/bimserver/shared/interfaces/AuthInterface.java)         |
| org.bimserver | MetaInterface     | [MetaInterface.java](https://github.com/opensourceBIM/BIMserver/blob/master/PluginBase/src/org/bimserver/shared/interfaces/MetaInterface.java)         |
| org.bimserver | PluginInterface   | [PluginInterface.java](https://github.com/opensourceBIM/BIMserver/blob/master/PluginBase/src/org/bimserver/shared/interfaces/PluginInterface.java)     |
| org.bimserver | ServiceInterface  | [ServiceInterface.java](https://github.com/opensourceBIM/BIMserver/blob/master/PluginBase/src/org/bimserver/shared/interfaces/ServiceInterface.java)   |
| org.bimserver | SettingsInterface | [SettingsInterface.java](https://github.com/opensourceBIM/BIMserver/blob/master/PluginBase/src/org/bimserver/shared/interfaces/SettingsInterface.java) |

# Access

Access to these methods is provided through 3 different channels: [Protocol Buffers](Protocol-buffers.md), [SOAP](SOAP.md) and [JSON](JSON-API.md).
