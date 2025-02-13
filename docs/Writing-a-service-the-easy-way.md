[This](Service-plugin.md) page has a description how to write internal services, but as most internal services seem to either checkin an updated revision, or add extended data, some convenience classes have been written that make it a lot easier to write an internal service. This page describes how to use those classes.

Make sure to create a [plugin.xml](Plugin-development.md#so-how-to-develop-a-plugin)

# A service that adds extended data

These services are triggered by a new revision, and add extended data to the revision.

First subclass "AbstractAddExtendedDataService", which can be found in the package "org.bimserver.plugins.services" in the "Shared" project.

- Create a constructor and call the super constructor with 2 arguments, name and description.
- Implement [newRevision](https://github.com/opensourceBIM/BIMserver/blob/master/Shared/src/org/bimserver/plugins/services/AbstractService.java#L88)
- (Optional) Implement [getProgressType](https://github.com/opensourceBIM/BIMserver/blob/master/Shared/src/org/bimserver/plugins/services/AbstractService.java#L94) If you want to report progress-data, the default is UNKNOWN
- (Optional) Implement [getSettingsDefinition](https://github.com/opensourceBIM/BIMserver/blob/master/Shared/src/org/bimserver/plugins/Plugin.java#L64) to tell BIMserver you require certain user-defined settings

Full code:

```
package org.bimserver.demoplugins.service;

import org.apache.commons.io.IOUtils;
import org.bimserver.interfaces.objects.SObjectType;
import org.bimserver.plugins.services.AbstractAddExtendedDataService;
import org.bimserver.plugins.services.BimServerClientInterface;

public class HtmlService extends AbstractAddExtendedDataService {
        // A unique namespace, this is used by other software to determine the type of file you uploaded as extended data
	private static final String NAMESPACE = "htmldemo";

        // Constructor, make sure it is a no-arg constructor
	public HtmlService() {
                // Give a sensible name and description for the service
		super("HTML Demo Service", "HTML Demo Service", NAMESPACE);
	}

        // This is the method that gets called when there is a new revision, have a look at the [documentation](https://github.com/opensourceBIM/BIMserver/blob/master/Shared/src/org/bimserver/plugins/services/AbstractService.java#L92) for the details
	@Override
	public void newRevision(RunningService runningService, BimServerClientInterface bimServerClientInterface, long poid, long roid, String userToken, long soid, SObjectType settings) throws Exception {
		byte[] bytes = IOUtils.toByteArray(getPluginContext().getResourceAsInputStream("data/example.html"));
		addExtendedData(bytes, "example.html", "HTML Demo Results", "text/html", bimServerClientInterface, roid, NAMESPACE);
	}

        // (Optional) Method to let BIMserver know whether you are going to provide progress-data, only required to implement if progress will be KNOWN
	@Override
	public ProgressType getProgressType() {
		return ProgressType.KNOWN;
	}
}
```

# A service that checks in a new revision

TODO

# Traversing a model

TODO
