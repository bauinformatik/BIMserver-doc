BIMserver allows for extending the BIMserver functionalities by creating plugins. These plugins however are always running within a BIMserver and consequently must be written in Java. To allow for 3rd parties to write software in other languages and software running on other servers, the concept of services and notifications has been added.

# Notifications

Notifications are JSON messages being send to listening web servers. They will be sent on certain events, such as a user checking in a new revision or extendeddata being added to a project or revision.

To be consistent, notifications are being sent just like normal BIMserver API calls. So for example the notification of a new revision looks like this:
```
{
  request: {
    interface: "Bimsie1RemoteServiceInterface",
    method: "newRevision",
    arguments: {
        poid: 12345,
        roid: 123,
        serviceIdentifier: "Furniture Placer",
        profileIdentifier: "Furniture Placer",
        userToken: "ABC",
        token: "CDE",
        apiUrl: "http://localhost:8080/json"
    }
  }
}
```

In this example, the poid and roid correspond to the project and new revision. The serviceIdentifier should be used to differentiate between different kinds of services being hosted by the same server. The profileIdentifier can be used to use specific settings. The userToken can be used to identify a certain user on the server the service is running on. The token and apiUrl are optional and can - if provided - allow a service to do API calls to the originating BIMserver. In typical cases the service will use these credentials to download the specific revision.

# A typical workflow

1. A service must be provided somewhere. This means a webserver has to run, and JSON messages being posted must be processed correctly and according to the Bimsie1RemoteServiceInterface interface.
2. The user calls ServiceInterface.addServiceToProject. The SService object is described [here]
3. A user checks in a new revision
4. The service will be triggered, a JSON message will be send to the address provided by the service provider
5. The service will will usually download the revision from the originating BIMserver and do some things with it

# Bimsie1RemoteServiceInterface

This interface contains all methods a service provider should implement.

## getService

This method provides the caller with information about a specific service.
The fields:

| Name | Type | Description |
| ------------- |-------------| -----|
| name | String | The user-readable name of this service |
| url | String | The URL to which the notifications should be send |
| token | String| |
| identifier | String |
| notificationProtocol | SAccessMethod | The protocol that should be used for notifications, should always be "json" |
| description | String | A user-readable description of this service |
| trigger | STrigger | What should trigger this service (can be NEW_REVISION, NEW_PROJECT or NEW_EXTENDED_DATA) |
| readRevision | boolean | Whether this service requires reading the revision |
| readExtendedData | String | An optional ExtendedDataSchema namespace this service requires reading |
| writeRevision | boolean | Whether this service requires checking in a new revision |
| writeExtendedData | String|An optional ExtendedDataSchema namespace for which this service needs to write data |
| providerName | String | User-readable name of the provider of this service |
| companyUrl | String | URL to company website |
| tokenUrl | String | URL to page where a user can create a token (usually some kind of register page) |
| newProfileUrl | String | URL to page where a user can create a new profile |

## getPrivateProfiles

Arguments:

| Name | Type | Description |
| ------------- |-------------| -----|
| serviceIdentifier | String | Identifier of the service, should be unique per server |
| token | String | A token the service provider can use to identify a certain user |

Result:

A List of SProfileDescriptor:

| Name | Type | Description |
| ------------- |-------------| -----|
| name | String | User-readable name of the profile |
| description | String | Description of this profile |
| publicProfile | boolean | Whether this profile is public (can be used without a token) |
| identifier | String | An identifier for this profile |

## getPublicProfiles

Arguments:

| Name | Type | Description |
| ------------- |-------------| -----|
| serviceIdentifier | String | Identifier of the service, should be unique per server |

Result:

A List of [SProfileDescriptor]

## newRevision

Called when a new revision has been added.

| Name | Type | Description |
| ------------- |-------------| -----|
| poid| long | Project on which the new revision was added |
| roid| long | Revision that has been created |
| soid| long | User-specific identifier of the service on the BIMserver (can be used to gather settings) |
| serviceIdentifier| String | Unique service identifier |
| profileIdentifier| String | Unique profile identifier |
| userToken| String | A token the service provider can use to identify a certain user |
| token| String | Optional token that can be used to connect to the originating BIMserver (see apiUrl) |
| apiUrl| String | URL of the API of the originating BIMserver |

## newExtendedDataOnProject

Called when new extended data has been added to a project

| Name | Type | Description |
| ------------- |-------------| -----|
| poid| long | Project on which the new extended data was added |
| edid| long | Object ID of the extended data that has been added |
| soid| long | User-specific identifier of the service on the BIMserver (can be used to gather settings) |
| serviceIdentifier| String | Unique service identifier |
| profileIdentifier| String | Unique profile identifier |
| userToken| String | A token the service provider can use to identify a certain user |
| token| String | Optional token that can be used to connect to the originating BIMserver (see apiUrl) |
| apiUrl| String | URL of the API of the originating BIMserver |

## newExtendedDataOnRevision

Called when new extended data has been added to a revision

| Name | Type | Description |
| ------------- |-------------| -----|
| poid| long | Project on which the new revision was added |
| roid| long | Revision that has been created |
| edid| long | Object ID of the extended data that has been added |
| soid| long | User-specific identifier of the service on the BIMserver (can be used to gather settings) |
| serviceIdentifier| String | Unique service identifier |
| profileIdentifier| String | Unique profile identifier |
| userToken| String | A token the service provider can use to identify a certain user |
| token| String | Optional token that can be used to connect to the originating BIMserver (see apiUrl) |
| apiUrl| String | URL of the API of the originating BIMserver |

# Libraries

To make working with notifications easier, we have developed extensions for the existing BIMserver libraries

| Language | URL |
| --- | --- |
| Java | <https://github.com/opensourceBIM/BIMserver/tree/master/BimServerClientLib> |
| JavaScript | <https://github.com/opensourceBIM/BIMserver/blob/master/BimServer/www/js/bimserverapi.js> |
| PHP | <https://github.com/opensourceBIM/phpClientLib> |