# Current serializers (used by BIMvie.ws and BIMsurfer)

## Binary serializers

These serializers write the geometry in a binary format that in most cases can easily be send to GPU's.

The source code can be found in the [BinarySerializers plugin repository](https://github.com/opensourceBIM/BinarySerializers) and should be quite self explanatory.

Because at this time, JavaScript is not able to load binary data in a streaming way, there is also a messaging implementation. This implementation sends the geometry in batches via WebSocket: [BinaryGeometryMessagingSerializer.java](https://github.com/opensourceBIM/BinarySerializers/blob/master/BinarySerializers/src/org/bimserver/serializers/binarygeometry/BinaryGeometryMessagingSerializer.java). 

These serializers are used by BIMvie.ws and BIMsurfer. The non-messaging serializers are not used any more and have been removed.

## JSON serializers

To be updated


# Older serializers

## SceneJsShellSerializer

This serializes the semantic information of the IFC model in JSON format. This contains no geometry. It's used by the sidebar showing the tree etc...

Example files:
* [Test 1 Shell.json](https://raw.githubusercontent.com/opensourceBIM/BIMserver/master/Documentation/files/Test%201%20Shell.json)
* [Test 2 Shell.json](https://raw.githubusercontent.com/opensourceBIM/BIMserver/master/Documentation/files/Test%202%20Shell.json)
* [Test 3 Shell.json](https://raw.githubusercontent.com/opensourceBIM/BIMserver/master/Documentation/files/Test%203%20Shell.json)
* [Test 4 Shell.json](https://raw.githubusercontent.com/opensourceBIM/BIMserver/master/Documentation/files/Test%204%20Shell.json)
* [Test 5 Shell.json](https://raw.githubusercontent.com/opensourceBIM/BIMserver/master/Documentation/files/Test%205%20Shell.json)

## JsonGeometrySerializer

Serializes the geometry in JSON. Right now this serializer is being called for every (enabled and existing) IfcProduct subtype.

Example files:
* [Test 1.json](https://raw.githubusercontent.com/opensourceBIM/BIMserver/master/Documentation/files/Test%201.json)
* [Test 2.json](https://raw.githubusercontent.com/opensourceBIM/BIMserver/master/Documentation/files/Test%202.json)
* [Test 3.json](https://raw.githubusercontent.com/opensourceBIM/BIMserver/master/Documentation/files/Test%203.json)
* [Test 4.json](https://raw.githubusercontent.com/opensourceBIM/BIMserver/master/Documentation/files/Test%204.json)
* [Test 5.json](https://raw.githubusercontent.com/opensourceBIM/BIMserver/master/Documentation/files/Test%205.json)

## Even older serializers

The older serializers "SceneJSSerializer" and "StreamingSceneJSSerializer" are not used anymore.
