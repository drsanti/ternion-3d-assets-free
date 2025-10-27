# ternion-3d-assets-free

## Repository

https://github.com/drsanti/ternion-3d-assets-free

## How to use:

### Next.js applications

- Copy the `assets` directory into the `./public/assets` directory

### Next.js example

```
npm install @ternion/t3d
```

```ts
"use client";
import { useEffect } from "react";
const MODEL = "assets/models/scenes/dev_scenes.glb";
export default function Example() {
  useEffect(() => {
    const loadT3D = async () => {
      const { T3D } = await import("@ternion/t3d");
      const engine = new T3D();
      engine.loadModel(MODEL).then((model) => {
        engine.createBlenderScene(model);
      });
      return () => engine.dispose();
    };

    loadT3D();
  }, []);

  return (
    <div className="flex h-screen w-screen justify-center items-center bg-black text-white text-5xl">
      T3D Engine Example
    </div>
  );
}
```
