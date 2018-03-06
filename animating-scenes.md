## Animating Scenes

Animate your scenes by updating the properties of their components:

```js
// Update the rotation angle
entity.transform.angle = 90.0;
```

Let's start our torus spinning:

```js
entity.scene.on("tick", function () {
    entity.transform.angle += 0.5;
});
```

  


