This page tries to explain why your database grows the way it does.

# Models

Obviously Building Information Models take up most of the space. BIMserver does versioning based on revisions. This means that every object will be stored with information about the project it belongs to, the revision it belongs to and it's own identifier.

When you are uploading multiple IFC files to the same project, all data will be stored. Even if only small parts of the model have changed, every object is stored again. The reason for this is that it's impossible for BIMserver to figure out what has changed, and which object have stayed the same.

That's why we also added the [low level calls](Low-level-calls.md), to actually change models by telling what has changed, instead of telling what the new complete model looks like.

# Geometry

In IFC (Industry Foundation Classes), 3D model geometry can be represented in various complex ways such as extrusion, bounding box, B-splines, triangles, and more. However, most 3D software and viewers can only handle one type of geometry: triangles.

To display these models correctly, we need to convert all the different types of geometry into triangles. This conversion process is handled by the "render engine" in BIMserver, typically using a library like [IfcOpenShell](https://github.com/IfcOpenShell/IfcOpenShell). However, this conversion can be time-consuming.

To avoid repeated conversions every time a model is viewed, we store the converted triangle data directly in the database. The amount of data needed for this depends on the complexity and types of geometry present in the IFC file.

# Other data

Apart from the Building Information Models BIMserver also stores information about projects, revisions, users, checkouts, history etc... This data is also being versioned, but on a per-object basis. So if you change the name of a project, the project (object) will be stored again in the database with a new revision number. Usually these objects don't take up too much space, but certain access patterns can definitely make this explode.
