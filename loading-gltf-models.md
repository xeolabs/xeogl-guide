## Loading glTF Models

* [Overview](#overview)
* [Supported glTF 2.0 features](#supported-features)
* [Loading glTF](#loading-gltf)
* [Loading options](#loading-options)
* [Finding loaded Entities](#finding-loaded-entities)
* [Transforming a GLTFModel](#transforming-a-gltfmodel)
* [Getting the World-space boundary of a GLTFModel](#getting-the-world-space-boundary-of-a-gltfmodel)
* [Clearing a GLTFModel](#clearing-a-gltfmodel)
* [Destroying a GLTFModel](#destroying-a-gltfmodel)

## Overview

* A GLTFModel is a container of [Components](http://xeogl.org/docs/classes/Component.html) that loads itself from a glTF file.
* It begins loading as soon as you set its ````src````
 property to the location of a valid glTF file.
* You can set ````src```` to a new file path at any time, which causes
 the GLTFModel to clear itself and load components from the new file.

It inherits these capabilities from its [Model](http://xeogl.org/docs/classes/Model.html) base class:

* Allows you to access and manipulate the components within it.
* Can be transformed within World-space by attaching it to a [Transform](http://xeogl.org/docs/classes/Transform.html).
* Provides its dynamic World-space axis-aligned boundary.

## Supported glTF 2.0 features

So far, GLTFModel loads only geometry, materials and modeling transform hierarchies, without animations. It does not load cameras or lights because its purpose is to import models into environments that have already been created using the xeogl API.

In addition to glTF's core metal-roughness material workflow, GLTFModel also supports two material extensions:

* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/Khronos/KHR_materials_pbrSpecularGlossiness/README.md)
* [KHR_materials_common](https://github.com/KhronosGroup/glTF/blob/master/extensions/Khronos/KHR_materials_common/README.md)

### Loading glTF

Load a glTF file by creating a GLTFModel:

````javascript
var model = new xeogl.GLTFModel({
    id: "gearbox",
    src: "models/gltf/gearbox_conical/scene.gltf",
});
````

A GLTFModel prefixes its own ID to those of its components. The ID is optional, but in this example we're providing our own custom ID.

The GLTFModel begins loading the glTF file immediately. 

To bind a callback to be notified when the file has loaded (which fires immediately if already loaded):
 
````javascript
model.on("loaded", function() {
    // GLTFModel has loaded!
});
````

You can also bind a callback to fire if loading fails:

````javascript
model.on("error", function(msg) {
       // Error occurred
});
````

To switch to a different glTF file, simply update ````src````:

````javascript
model.src = "models/gltf/Buggy/glTF/Buggy.gltf"
````

### Loading options

The following options may be specified when loading glTF:

| Option | Type | Range | Default Value | Description |
|:--------:|:----:|:-----:|:-------------:|:-----:|:-----------:|
| flattenTransforms | Boolean |  | true | Flattens transform hierarchies to improve rendering performance. |
| lambertMaterials | Boolean |  | false | When true, gives each [http://xeogl.org/docs/classes/Entity.html](Entity) the same [http://xeogl.org/docs/classes/LambertMaterial.html](LambertMaterial) and a ````colorize```` property set the to diffuse RGB color extracted from the glTF material. This is typically used for CAD models with huge amounts of objects, and will ignore textures.|
| quantizeGeometry | Boolean |  | true | When true, quantizes geometry to reduce memory and GPU bus usage (see [Geometry](http://xeogl.org/docs/classes/Geometry.html)). |
| combineGeometry | Boolean |  | true | When true, combines geometry vertex buffers to improve rendering performance (see [Geometry](http://xeogl.org/docs/classes/Geometry.html)). |
| backfaces | Boolean |  | true | When true, allows visible backfaces, wherever specified in the glTF. When false, ignores backfaces. |
| ghost | Boolean |  | false | When true, ghosts all the model's Entities (see [http://xeogl.org/docs/classes/Entity.html](Entity) and [http://xeogl.org/docs/classes/GhostMaterial.html](GhostMaterial)). |
| outline | Boolean |  | false | When true, outlines all the model's Entities (see [http://xeogl.org/docs/classes/Entity.html](Entity) and [http://xeogl.org/docs/classes/OutlineMaterial.html](OutlineMaterial)). |
| highlight | Boolean |  | false | When true, highlights all the model's Entities (see [http://xeogl.org/docs/classes/Entity.html](Entity)). |
| ghostEdgeThreshold | Number | [0..180] | 2 | When ghosting, this is the threshold angle between normals of adjacent triangles, below which their shared wireframe edge is not drawn. |
| maxEntities | Number | | | Optional maximum number of [http://xeogl.org/docs/classes/Entity.html](Entity)'s to load. |
| included | Function(entityId) | | null | Optional callback to mask which [http://xeogl.org/docs/classes/Entity.html](Entity)'s are loaded. Entity will only be loaded when this callback returns ````true``` for the given Entity ID. |

Using the ````flattenTransforms```` option to load a glTF model while flattening its transform hierarchy:

````javascript
var model = new xeogl.GLTFModel({
    id: "gearbox",
    src: "models/gltf/gearbox_conical/scene.gltf",
    flattenTransforms: true
});
````

Using the ````included```` option to load all entities except for those with IDs "gearbox#77.0" and "gearbox#79.0":

````javascript
var model = new xeogl.GLTFModel({
    id: "gearbox",
    src: "models/gltf/gearbox_conical/scene.gltf",
    included: function(entityId) {
       return id !== ("gearbox#77.0") &&  (id !== "gearbox#79.0");
    }
});
````

### Finding GLTFModels in Scenes

Our GLTFModel will now be registered by ID on its Scene, so we can now find it like this:

````javascript
model = xeogl.scene.models["gearbox"];
````

That's assuming that we've created the GLTFModel in the default xeogl Scene, which we're doing in these examples.

### Finding loaded Entities

Once the GLTFModel has loaded, its [http://xeogl.org/docs/classes/Scene.html](Scene) will contain various components that represent the elements of the glTF file.
We'll now access some of those components by ID, to query and update them programmatically.

Let's highlight a couple of Entities in our GLTFModel:

````javascript
var entities = scene.entities;

entities["gearbox77.0"].highlight = true;
entities["gearbox79.0"].highlight = true;
````

A GLTFModel also has ID maps of the components within it. Its components map contains all its [Components](http://xeogl.org/docs/classes/Component.html) in one big map:

````javascript
model.components["gearbox77.0"].highlight = true;
````

while its entities map contains just the Entities:

````javascript
model.entities["gearbox77.0"].highlight = true;
````

Note the format of the [http://xeogl.org/docs/classes/Entity.html](Entity) IDs:

````<GLTFModel ID>#<glTF node ID>.<glTF primitive index>````

Within the glTF, a node's mesh may contain multiple primitives. For each primitive, xeogl will create a separate [http://xeogl.org/docs/classes/Entity.html](Entity). Within each Entity's ID, the part before the hash is the ID of the GLTFModel, followed by the ID of the node, then ".", then the index of the primitive within the mesh.

### Transforming a GLTFModel

A GLTFModel lets us transform its Entities as a group.

We can attach a modeling [Transform](http://xeogl.org/docs/classes/Transform.html) to our GLTFModel, as a either a configuration object or a component instance:

````javascript
// Attach transforms as a configuration object:
model.transform = {
    type: "xeogl.Translate",
    xyz: [-35, 0, 0],
    parent: {
        type: "xeogl.Rotate",
        xyz: [0, 1, 0],
        angle: 45
    }
};

// Attach our own transform instances:
model.transform = new xeogl.Translate({
    xyz: [-35, 0, 0],
    parent: new xeogl.Rotate({
        xyz: [0, 1, 0],
        angle: 45
    })
});
````

We can also provide the [Transform](http://xeogl.org/docs/classes/Transform.html) to the GLTFModel constructor, as either configuration objects or instances.

Here we'll provide a Transform hierarchy as a configuration object:

````javascript
// Model internally instantiates our transform components:
var model = new xeogl.GLTFModel({
    src: "models/gltf/gearbox_conical/scene.gltf",
    transform: {
       type: "xeogl.Translate",
       xyz: [-35, 0, 0],
       parent: {
           type: "xeogl.Rotate",
           xyz: [0, 1, 0],
           angle: 45
       }
    }
 });
````

### Getting the World-space boundary of a GLTFModel

Get the World-space axis-aligned boundary like this:

````javascript
model.on("boundary", function() {
   var aabb = model.aabb; //  [xmin, ymin,zmin,xmax,ymax, zmax]
   //...
});
````

We can also subscribe to changes to that boundary, which will happen whenever

* the GLTFModel's [Transform](http://xeogl.org/docs/classes/Transform.html) is updated,
* components are added or removed, or
* the GLTF model is reloaded from a different source,
* the [http://xeogl.org/docs/classes/Geometry.html](Geometries) or [http://xeogl.org/docs/classes/Transform.html](Transforms) of its [http://xeogl.org/docs/classes/Entity.html](Entities) are updated.

````javascript
model.on("boundary", function() {
   var aabb = model.aabb; // [xmin, ymin,zmin,xmax,ymax, zmax]
});
````

### Clearing a GLTFModel

```` Javascript
model.clear();
````

### Destroying a GLTFModel

```` Javascript
model.destroy();
````

## Examples

* [Damaged Helmet with metal/rough PBR materials](../../examples/#importing_gltf_DamagedHelmet)
* [Hover bike with specular/glossiness PBR materials](../../examples/#importing_gltf_Hoverbike)
* [Loading glTF with embedded assets](../../examples/#importing_gltf_embedded)
* [Parsing glTF JSON with embedded assets](../../examples/#importing_gltf_parsing_embedded)
* [Ignoring materials when loading](../../examples/#importing_gltf_options_ignoreMaterials)
* [Baking transform hierarchies when loading](../../examples/#importing_gltf_options_flattenTransforms)
* [Converting materials to simple Lambertian when loading](../../examples/#importing_gltf_options_lambertMaterials)
* [All loading options for max performance](../../examples/#importing_gltf_options_maxPerformance)