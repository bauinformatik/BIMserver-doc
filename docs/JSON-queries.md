> The new query language is still in development, so expect this to change.

You can read the original motivations to write this language [here](New-query-langage.md)

# Intro

Some characteristics to of this language to keep in mind:

- The data model of the "queried" is the same data model as the "query results". In most cases it will be Ifc2x3tc1 or Ifc4.
- The previous also explains why there are no aggregates (such as `COUNT`, `AVG`, `MAX`, etc. in SQL), because there is no place to store those.
- A query result is always a subset of the original model.
- Query language is probably not the best term here, a "filter language" might be better.
- The queries are based on objects. Geometry is not treated any other way. If you don't ask for geometry, you won't get it.

# JSON

All queries must be valid JSON. This means that all keys must be quoted, all strings must be quoted. When sending a query to BIMserver using the [ServiceInterface.download](https://github.com/opensourceBIM/BIMserver/blob/0c7e37092045d08a4ae8ca836026823f02a977b2/PluginBase/src/org/bimserver/shared/interfaces/ServiceInterface.java#L102-L116) method through JSON, ensure that your query is base64 encoded.

For further information regarding downloading the data via queries, please check [Downloading models](Downloading-models.md)

# Base query

This query will query all objects.

```json
{}
```

# Type query

This query will get all IfcWall objects

```json
{
  "type": "IfcWall"
}
```

When your model for example only contains IfcWallStandardCase objects, this query will not return any objects, although IfcWallStandardCase is a subtype of IfcWall. To include subtypes, you have to explicitly define this:

```json
{
  "type": "IfcWall",
  "includeAllSubtypes": true
}
```

You can also query multiple types, the next example would give you all IfcDoor and IfcWindow objects

```json
{
  "types": ["IfcDoor", "IfcWindow"]
}
```

# GUID query

When you already know the GUID(s) of (an) object(s), you can query those directly:

```json
{
  "guids": ["GUID1", "GUID2"]
}
```

# OID query

The same as for GUIDs, you can also query by OID (ObjectID)

```json
{
  "oids": [1523, 2130, 898]
}
```

# Properties

Every IFC object has a fixed set of fields. For example the IfcWindow has the fields "OverallWidth" and "OverallHeight" and [many more](https://standards.buildingsmart.org/IFC/RELEASE/IFC4/ADD2_TC1/HTML/link/ifcwindow.htm). To use properties that are not defined in the schema, every object can be extended with more properties by adding them using IfcPropertySet and IfcProperty. For example IfcWall objects that are external can be queried via the property `IsExternal` in property set `Pset_WallCommon`:

```json
{
  "type": "IfcWall",
  "includeAllSubtypes": true,
  "properties": {
    "Pset_WallCommon": {
      "IsExternal": true
    }
  }
}
```

# Serializing to IFC

In a lot of cases, the results of the query will be serialized as IFC. The previous examples would however not result in valid IFC files. For an IFC file to be valid, it not only has to solely contain objects that conform to the IFC schema, also certain references should always be included. For example every object must have an IfcOwnerHistory. Also there must always be an 1 IfcProject object.

Example of the body of an IFC file for the IfcWall query:

```
#1= IFCWALLSTANDARDCASE('1hmUg1hfv4UPf0UtHFe7ta',$,'SW - 009',$,$,$,$,'6BC1EA81-AE9E-4479-9A-40-7B744FA07DE4');
#2= IFCWALLSTANDARDCASE('05UYVl82vAa8mFc3FMTYL7',$,'SW - 010',$,$,$,$,'057A27EF-202E-4A90-8C-0F-9833D6762547');
#3= IFCWALLSTANDARDCASE('37ZZt1nzL2nu6PjSr0Wa2a',$,'SW - 011',$,$,$,$,'C78E3DC1-C7D5-42C7-81-99-B5CD408240A4');
#4= IFCWALLSTANDARDCASE('1EzyXtTtv7RAinwkFVr6_I',$,'SW - 012',$,$,$,$,'4EF7C877-777E-476C-AB-31-EAE3DFD46F92');
#5= IFCWALLSTANDARDCASE('08u2EMNSnBL8Izk3LNHAwK',$,'SW - 013',$,$,$,$,'08E02396-5DCC-4B54-84-BD-B8355744AE94');
```

The above IFC file is invalid because none of the IfcWallStandardCase entities references an IfcOwnerHistory, also no IfcProject is there. Also note that no geometry is present, so you won't see anything in an IFC viewer.

To make creating valid IFC files with queries easier, a few predefined includes are available for the IFC versions supported in BIMserver.
For every IFC schema, there is a respective "standard library" holding these predefined includes, for example `ifc2x3-stdlib` and `ifc4-stdlib`.

To make the IfcWall example work in IFC4, this would be the new query:

```json
{
  "type": "IfcWall",
  "includeAllSubtypes": true,
  "includes": [
    "ifc4-stdlib:ContainedInStructure",
    "ifc4-stdlib:OwnerHistory",
    "ifc4-stdlib:Representation",
    "ifc4-stdlib:ObjectPlacement"
  ]
}
```

Each include has a different function.

## ifc4-stdlib:ContainedInStructure

ifc4-stdlib:ContainedInStructure will make sure all the spatial containment references "up" will be followed.

Example project structure

```
IfcProject
  IfcSite
    IfcBuilding
      IfcBuildingStorey
        IfcSpace
          IfcWall
```

For each IfcWall encountered, the tree will be "walked" all the way up. This automatically includes the IfcProject which is a requirement for a valid IFC file

## ifc4-stdlib:OwnerHistory

For every IfcWall, the OwnerHistory will be included

## ifc4-stdlib:Representation

For every IfcWall, the Representation will be included. This can be a very large network/tree of objects. Representation is what gives object geometry in a viewer

## ifc4-stdlib:ObjectPlacement

This makes sure that all objects will also include their placement, which put's the object's geometry in the right place.
