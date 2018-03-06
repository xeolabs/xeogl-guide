## Creating a 3D Scene

To get started, first include [xeogl.js](https://github.com/xeolabs/xeogl/tree/master/build/) in your HTML page:

```
<script src="xeogl.js"></script>
```

Then define your 3D scene as an entity-component graph, as shown below.

Your scene is basically a bunch of [components](http://xeogl.org/docs/classes/Component.html) that are tied together by [entities](http://xeogl.org/docs/classes/Entity.html). Each entity represents a WebGL draw call.

```js
var geometry = new xeogl.TorusGeometry({
    radius: 1.0,
    tube: 0.3
});

var material = new xeogl.MetallicMaterial({
    baseColorMap: new xeogl.Texture({
        src: "textures/diffuse/uvGrid2.jpg"
    }),
    roughnessMap: new xeogl.Texture({
        src: "textures/roughness/goldRoughness.jpg"
    })
});

var entity = new xeogl.Entity({
    geometry: geometry,
    material: material
});
```

Here's what we just created:

[![](http://localhost:8082/website/assets/images/example1.png)](http://xeogl.org/examples/#entities_examples_metallicTorus)

[![](http://localhost:8082/website/assets/images/conceptScene.png)](http://xeogl.org/examples/#importing_gltf_GearboxAssy)

