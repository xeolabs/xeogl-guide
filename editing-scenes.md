## Editing Scenes

You can dynamically edit most things in your scene at runtime. Create and destroy components, link or unlink them, update their properties etc.

Let's add [rotation](http://xeogl.org/docs/classes/Rotate.html) and [scale](http://xeogl.org/docs/classes/Scale.html) transforms to our torus, then increase its size:

```js
// Rotate and scale our torus entity
entity.transform = new xeogl.Rotate({
    xyz: [0,1,0],
    angle : 0,
    parent: new xeogl.Scale({
        xyz: [2, 2, 2]
    })
});

// Increase the size
entity.transform.parent.[1.5,  1.5, 1.5];
```



