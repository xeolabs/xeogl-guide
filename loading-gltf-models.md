## Loading glTF Models

Load 3D models into your scene from glTF:

```
var model = new xeogl.GLTFModel({
    id: "office",
    src: "models/gltf/office/scene.gltf"
});
```

Then animate and navigate their entities to create custom visualizations:

```
model.on("loaded", function() {

    // When model loaded, highlight its furniture
    // furniture entities and fit to view

    var entityIds = [
        "office#79.0", "office#31.0", "office#105.0",
        "office#8.0", "office#65.0", "office#65.0" //....
    ];

    for (var i = 0; i < entityIds.length; i++) {
        var id = entityIds[i];
        var entity = model.entities[id];

        entity.highlight = true;
    }

    var cameraFlight = new xeogl.CameraFlightAnimation();
    cameraFlight.flyTo(model);
});

// Control camera with mouse and touch
var cameraControl = new xeogl.CameraControl();
```

Here's what we just created:

[![](http://localhost:8082/assets/images/screenshots/officePlanHighlighted.png)](http://xeogl.org/examples/#importing_gltf_OfficePlan)

_Model by _[_sketchfab.com/scann3d_](https://sketchfab.com/scann3d)

