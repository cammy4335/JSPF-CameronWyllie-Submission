# Element 5
In Element 5 I have included the following: 
* Multiple Objects
* Merged Meshes
* Arc Rotate Camera
* Player Movement with Animation
* Havok Physics
* Sound and Music
* Menu Buttons
* Example of Motion and Animation
* Terrain and Skybox
* Use of Textures and Assets 
* Use of Lighting

# Code snippets


### Code 1 - Havok Inizitialization (physics)
This code is using the Babylon.js framework to integrate the Havok Physics engine for handling physics simulations in a 3D scene. 

1. Importing Modules\
The code imports necessary modules from the Babylon.js framework and the Havok Physics engine. This includes the "HavokPhysics" object and specific classes and types related to physics.
```typescript
import HavokPhysics from "@babylonjs/havok";
import { HavokPlugin, PhysicsAggregate, PhysicsShapeType } from "@babylonjs/core";
```
2. Initializing Havok Physics\
This part of the code initializes Havok Physics asynchronously. The "HavokPhysics" function is called, which returns a promise. When the promise resolves, the "then" callback is executed, and the "havok" object is assigned to the "initializedHavok" variable.
```typescript
let initializedHavok;
HavokPhysics().then((havok) => {
  initializedHavok = havok;
});
```
3. Creating Havok Plugin\
Here, another instance of Havok Physics is created using "HavokPhysics()", and a new "HavokPlugin" is instantiated with "true" as the first argument (enabling debugging features) and the obtained "havokInstance".
```typescript
const havokInstance = await HavokPhysics();
const havokPlugin = new HavokPlugin(true, havokInstance);
```
4. Setting up Physics in the Scene\
This section sets up physics in the Babylon.js scene. It assigns the Havok Physics instance to "globalThis.HK" (globally accessible), and then the "enablePhysics" method of the scene is called. It takes a gravity vector ("new Vector3(0, -9.8, 0)") and the "havokPlugin" as parameters to enable physics in the scene.
```typescript
globalThis.HK = await HavokPhysics();
that.scene.enablePhysics(new Vector3(0, -9.8, 0), havokPlugin);
```

### Code 2 - Player Functions and action manager
#### importPlayerMesh Function
This code defines functions related to a 3D player character in a Babylon.js scene, including importing a player mesh, handling user input for movement, managing animations, detecting collisions with a collider mesh, and setting up an action manager for keyboard input.
1. Variables\
Defines variables for tracking key presses, current speed, walking speed, and running speed.
```typescript
let keyDownMap: any[] = [];
let currentSpeed: number = 0.1;
let walkingSpeed: number = 0.1;
let runningSpeed: number = 0.4;
```
2. Function Definition\
Defines the importPlayerMesh function that takes parameters like the scene, collider mesh, initial Y and Z positions, and a scale factor.
```typescript
function importPlayerMesh(scene: Scene, collider: Mesh, y: number, z: number, scaleFactor: number) {
```

3. Mesh Import\
Imports a player mesh using Babylon.js SceneLoader.ImportMesh.
```typescript
let item: any = SceneLoader.ImportMesh("", "./models/", "dummy3.babylon", scene, function(newMeshes, particleSystems, skeletons, animationGroups) {
```
4. Mesh Setup\ 

Modifies the scale of the imported mesh and sets up animations.
```typescript
// ... (mesh and skeleton setup code)
mesh.scaling = new Vector3(scaleFactor, scaleFactor, scaleFactor);
```
5. User Input and Animation\ 
Handles user input (W, A, S, D keys) for movement, adjusts the player's position, and manages animations (idle and walking).
```typescript

 {
    let tempItem = { flag: false } 
    let item: any = SceneLoader.ImportMesh("", "./models/", "dummy3.babylon", scene, function(newMeshes, particleSystems, skeletons) {
      let mesh = newMeshes[0];
      let skeleton = skeletons[0];
      skeleton.animationPropertiesOverride = new AnimationPropertiesOverride();
      skeleton.animationPropertiesOverride.enableBlending = true;
      skeleton.animationPropertiesOverride.blendingSpeed = 0.05;
      skeleton.animationPropertiesOverride.loopMode = 1; 

      let walkRange: any = skeleton.getAnimationRange("YBot_Walk");

      let animating: boolean = false;

      scene.onBeforeRenderObservable.add(()=> {
        let keydown: boolean = false;
        let shiftdown: boolean = false;
        if (keyDownMap["w"] || keyDownMap["ArrowUp"]) {
          mesh.position.z += 0.1;
          mesh.rotation.y = 0;
          keydown = true;
        }
        if (keyDownMap["a"] || keyDownMap["ArrowLeft"]) {
          mesh.position.x -= 0.1;
          mesh.rotation.y = 3 * Math.PI / 2;
          keydown = true;
        }
        if (keyDownMap["s"] || keyDownMap["ArrowDown"]) {
          mesh.position.z -= 0.1;
          mesh.rotation.y = 2 * Math.PI / 2;
          keydown = true;
        }
        if (keyDownMap["d"] || keyDownMap["ArrowRight"]) {
          mesh.position.x += 0.1;
          mesh.rotation.y = Math.PI / 2;
          keydown = true;
        }
        if (keyDownMap["Shift"] || keyDownMap["LeftShift"]) {
          currentSpeed = runningSpeed;
          shiftdown = true;
        } else {
          currentSpeed = walkingSpeed;
          shiftdown = false;
        }

        if (keydown) {
          if (!animating) {
            animating = true;
            scene.beginAnimation(skeleton, walkRange.from, walkRange.to, true);
          }
        } else {
          animating = false;
          scene.stopAnimation(skeleton);
        }

  }
```

6. Collision Handling\
Detects collisions with a collider mesh and takes appropriate actions, such as logging a message and calling increaseScore().
```typescript
//collision
        if (mesh.intersectsMesh(collider)) {
          console.log("COLLIDED");
        }
      });
```
7. Physics Collision Setup\
Sets up physics collision using the Babylon.js physics engine.
```typescript
#
    //physics collision
      item = mesh;
      let playerAggregate = new PhysicsAggregate(item, PhysicsShapeType.CAPSULE, { mass: 0 }, scene);
      playerAggregate.body.disablePreStep = false;

    });
    return item;
```

8. Return Statement\
Returns the created player mesh.
```typescript
return item;
```

### Code 3 - Start Button
1. "createSceneButton" Function\
The function takes several parameters:\
+ "scene": Represents the 3D scene where the button will be added.\
+ "name": A string representing the name of the button.\
+ "index": A string representing an index for the button.\
+ "x" and "y": Strings representing the X and Y positions of the button.\
+"advtex": Some variable (possibly a GUI advanced texture) where the button will be added.
```typescript
function createSceneButton(
  scene: Scene,
  name: string,
  index: string,
  x: string,
  y: string,
  advtex
) {
```
2. Creating the Button\
This line creates a GUI button using the Babylon.js Button.CreateSimpleButton method, providing the specified name and index.
```typescript
let button = GUI.Button.CreateSimpleButton(name, index);
```
3. Setting Button Properties\
These lines set various properties for the button, such as width, height, text color, background color, font size, corner radius, and thickness.
```typescript
    button.left = x;
    button.top = y;
    button.width = "160px";
    button.height = "60px";
    button.color = "white";
    button.cornerRadius = 20;
    button.background = "green";
```
4. Adding Sound and Click Event\
This section creates a sound object ("buttonClick") for a menu click sound and associates it with a WAV file. It sets up an event listener for the button's "onPointerUp" event, logging a message to the console, playing the click sound, and calls button.visible so when the button is triggered the game scene is initialised.
```typescript
const buttonClick = new Sound("MenuClickSFX", "./audio/menu-click.wav", scene, null, {
  loop: false,
  autoplay: false,
});

    button.onPointerUpObservable.add(function () {
        console.log("THE GAME HAS STARTED");
        buttonClick.play();

        
        button.isVisible = false;
});
```

5. Adding Button to Advanced Texture\
This line adds the created button to the specified advanced texture ("advtex").
```typescript
advtex.addControl(button);
```
6. Return Statement
The function returns the created button.
```typescript
return button;
```

### Code 4 - Torus collectible rings
This code defines the function that creates a 3d mesh object shape called a torus

1. This line declares a function named createTorus that takes four parameters:
```typescript
  function createTorus(scene: Scene, px: number, py: number, pz: number) 
```

2. This line uses Babylon.js's MeshBuilder to create a torus mesh. The CreateTorus method is used to create the torus. The parameters are as follows:
 
    ```typescript
    const torus = MeshBuilder.CreateTorus("torus", {});
    ```

3. This line sets the position of the torus in 3D space using the position property. The Vector3 class is used to specify the X, Y, and Z coordinates, which are based on the values provided as px, py, and pz when the function is called
    
    ```typescript
    torus.position = new Vector3(px, py, pz);
     ```

4. This code registers an "after render" callback function using Babylon.js's registerAfterRender method. The function passed to registerAfterRender is executed after each frame is rendered in the scene. Inside this function:

    ```typescript
    scene.registerAfterRender(function () {
      torus.rotate(new Vector3(4, 8, 2)/*axis*/, 0.02/*angle*/, Space.LOCAL);
    });
    ```
5. This line returns the created torus mesh, allowing the caller of the createTorus function to use and manipulate the torus further if needed.

    ```typescript
    return torus;
    ```

### Code 5 - Cloning Torus collectible rings
This function clones the torus function and positions them around the enviornment

1. This line declares a function named cloneTorus that takes one parameter:

```typescript
function cloneTorus(scene: Scene): Mesh[] {
```

2. This line initializes an empty array torusInstances that will store references to the cloned torus meshes.

```typescript
const torusInstances: Mesh[] = [];
```

3. This code defines an array positions containing nine Vector3 objects. Each Vector3 represents the initial position of a torus instance in 3D space.

```typescript
const positions = [
  new Vector3(3, 0.5, 5),
  new Vector3(-3, 0.5, 1),
  new Vector3(0, 0.5, 0),
  new Vector3(3, 0.5, -3),
  new Vector3(-3, 0.5, -3),
  new Vector3(6, 0.5, 0),
  new Vector3(-6, 0.5, 0),
  new Vector3(0, 0.5, 6),
  new Vector3(0, 0.5, -6),
];
```

4. This loop iterates nine times, creating nine torus instances based on the positions defined in the positions array. For each iteration:

```typescript
for (let i = 0; i < 9; i++) {
  const position = positions[i];

  const torus = MeshBuilder.CreateTorus(`torus${i}`, {});
  torus.position = position;

  scene.registerAfterRender(function () {
    torus.rotate(new Vector3(4, 8, 2), 0.02, Space.LOCAL);
  });

  torusInstances.push(torus);
}
```

5.  the function returns the array torusInstances containing references to all the created torus instances.

```typescript
return torusInstances;

```

6. This line declares a function named checkCollision that takes two parameters:

```typescript
function checkCollision(cube: Mesh, torus: Mesh) {
```

7. This code checks if the cube mesh intersects with the torus mesh using the intersectsMesh method. If a collision is detected, it disposes of the cube mesh, effectively removing it from the scene which indicates that it has been collected.

```typescript
if (cube.intersectsMesh(torus)) {
  cube.dispose(); // Ring being removed to indicate collection
}
```

### Code 6 - Terrain
1. Material and Texture:\
A standard material (largeGroundMat) is created.A diffuse texture is assigned to the material, using an image of grass (valleygrass) from the given URL.

2. Creating Ground from Height Map:\
The MeshBuilder.CreateGroundFromHeightMap method is used to create a ground mesh based on a height map. The height map is specified with the URL "https://assets.babylonjs.com/environments/villageheightmap.png". Other parameters such as width, height, subdivisions, minHeight, and maxHeight are configured.
```typescript
  // Create a ground mesh from a height map
  const largeGround = MeshBuilder.CreateGroundFromHeightMap(
    "largeGround",
    "https://assets.babylonjs.com/environments/villageheightmap.png",
    { width: 150, height: 150, subdivisions: 20, minHeight: -0.01, maxHeight: 9.99 }
  );
```
3. Assigning Material:\
The created material (largeGroundMat) is assigned to the ground mesh (largeGround).
```typescript
  // Assign the material to the ground mesh
  largeGround.material = largeGroundMat;
```
4. Return the Ground Mesh:\
The function returns the created ground mesh.

```typescript
return largeGround;
```

5. Creating a Terrain Instance:\
Finally, an instance of the terrain is created in the Babylon.js scene using the createTerrain function, and it is assigned to that.largeGround. The resulting effect is a textured terrain based on the provided height map, displaying the selected texture valleygrass to the ground.



### Code 7 - Create Ground

1. This line declares a function named createGround that takes one parameter:

```typescript
function createGround(scene: Scene)
```

2. This line creates a ground mesh using MeshBuilder.CreateGround. The parameters for creating the ground are as follows:

```typescript
const ground: Mesh = MeshBuilder.CreateGround("ground", { width: 24, height: 24 });

```
3. This code sets up a physics simulation for the ground mesh using the Babylon.js physics engine. It creates a PhysicsAggregate for the ground:

```typescript
const groundAggregate = new PhysicsAggregate(ground, PhysicsShapeType.BOX, { mass: 0 }, scene);

```
4. This line creates a new standard material for the ground and names it "groundMat"

```typescript
const groundMat = new StandardMaterial("groundMat");

```
5. This code assigns a diffuse texture to the groundMat material. The texture is loaded from the URL "https://assets.babylonjs.com/environments/villagegreen.png".

```typescript
groundMat.diffuseTexture = new Texture("https://assets.babylonjs.com/environments/villagegreen.png");

```
6. This line sets the hasAlpha property of the diffuse texture to true

```typescript
groundMat.diffuseTexture.hasAlpha = true;

```
7. This line assigns the groundMat material to the ground mesh, giving it the appearance of the loaded texture.

```typescript
ground.material = groundMat;

```
8. This code sets the Y-coordinate of the ground mesh to 0.01. It elevates the ground slightly above the origin to avoid z-fighting (rendering artifacts) with other objects that may be at the exact same height.

```typescript
ground.position.y = 0.01;

```
9.  the function returns the created ground mesh, allowing the caller to use and manipulate it further in the scene.

```typescript
return ground;


```

### Code 8 - Skybox
1. Creating Skybox Mesh:\
The function uses MeshBuilder.CreateBox to create a box mesh named "skyBox" with a specified size of 150 units. This box will represent the skybox.

2. Creating Skybox Material:\
A StandardMaterial named "skyBox" is created for the skybox.

3. Back-face Culling:\
Back-face culling is disabled (backFaceCulling = false) to render both sides of the skybox.
```typescript
  // Disable back-face culling to render both sides of the skybox
  skyboxMaterial.backFaceCulling = false;
  ```
4. Reflection Texture (CubeTexture):\
A CubeTexture is created and set as the reflection texture for the skybox material. The texture is loaded from the "textures/skybox" directory.
```typescript
 // Set a CubeTexture as the reflection texture for the skybox
  skyboxMaterial.reflectionTexture = new CubeTexture("textures/skybox", scene);
   ```
5. Coordinates Mode for Reflection Texture:\
The coordinates mode for the reflection texture is set to Texture.SKYBOX_MODE.
```typescript
 // Set the coordinates mode for the reflection texture
  skyboxMaterial.reflectionTexture.coordinatesMode = Texture.SKYBOX_MODE;
```
6. Diffuse and Specular Colors:\
Diffuse and specular colors are set to black (Color3(0, 0, 0)) to avoid introducing additional colors to the reflections.
7. Assigning Material to Skybox Mesh:\
The skybox mesh is assigned the created material.
8. Return the Skybox Mesh:\
The function returns the created skybox mesh.

### Code 9 - Tress
this function is designed to create and manage a large number of tree sprites within a Babylon.js scene. 

1. This line declares a function named createTrees that takes one parameter:

```typescript
function createTrees(scene: Scene) 

```
2. This code initializes a SpriteManager named "treesManager" that will manage the tree sprites. The parameters are as follows:

```typescript
const spriteManagerTrees = new SpriteManager("treesManager", "textures/palmtree.png", 2000, { width: 512, height: 1024 }, scene);
```
3. This loop creates tree sprites with random positions within a specified range. For each iteration:

```typescript
//We create trees at random positions
for (let i = 0; i < 500; i++) {
    const tree = new Sprite("tree", spriteManagerTrees);
    tree.position.x = Math.random() * (-30);
    tree.position.z = Math.random() * 20 + 8;
    tree.position.y = 0.5;
}

```
4. This loop creates additional tree sprites with random positions within another specified range. For each iteration:

```typescript
for (let i = 0; i < 500; i++) {
    const tree = new Sprite("tree", spriteManagerTrees);
    tree.position.x = Math.random() * (25) + 7;
    tree.position.z = Math.random() * -35 + 8;
    tree.position.y = 0.5;
}

```
5. the function returns the spriteManagerTrees, which contains all the created tree sprites. This allows the caller to manage and manipulate the tree sprites as needed within the scene.

```typescript
return spriteManagerTrees;

```

### Code 10 - Cloned Houses
This code defines a series of functions to create and manipulate 3D house meshes in a scene. Let's break down each part of the code:

1. createBox Function:
+ Creates a box mesh with a specified width.
+ Sets different textures for the box material based on the width.
+ Defines face UV coordinates to map textures to different sides of the box.
+ Returns the created box.

```typescript

const faceUV = [];
    if (width == 2) {
        faceUV[0] = new Vector4(0.6, 0.0, 1.0, 1.0); //rear face
        faceUV[1] = new Vector4(0.0, 0.0, 0.4, 1.0); //front face
        faceUV[2] = new Vector4(0.4, 0, 0.6, 1.0); //right side
        faceUV[3] = new Vector4(0.4, 0, 0.6, 1.0); //left side
    }
    else {
        faceUV[0] = new Vector4(0.5, 0.0, 0.75, 1.0); //rear face
        faceUV[1] = new Vector4(0.0, 0.0, 0.25, 1.0); //front face
        faceUV[2] = new Vector4(0.25, 0, 0.5, 1.0); //right side
        faceUV[3] = new Vector4(0.75, 0, 1.0, 1.0); //left side
    }
```
2. createRoof Function:
+ Creates a cylinder mesh to represent a roof.
+ Assigns a roof texture to the material.
+ Adjusts scaling, rotation, and position properties.
+ Returns the created roof.
```typescript
  function createRoof(scene: Scene, width: number) {
    const roofMat = new StandardMaterial("roofMat");
    roofMat.diffuseTexture = new Texture("https://assets.babylonjs.com/environments/roof.jpg");

    const roof = MeshBuilder.CreateCylinder("roof", {diameter: 1.3, height: 1.2, tessellation: 3});
    roof.material = roofMat;
    roof.scaling.x = 0.75;
    roof.scaling.y = width;
    roof.rotation.z = Math.PI / 2;
    roof.position.y = 1.22;
    
    return roof;
  }
```
3. createHouse Function:
+ Combines a box and a roof into a single mesh using Mesh.MergeMeshes.
+ Sets the position and rotation of the house.
+ Returns the created house.
```typescript
 function createHouse(scene: Scene, width: number) {
    const box = createBox(scene, width);
    const roof = createRoof(scene, width);
    const house: any = Mesh.MergeMeshes([box, roof], true, false, undefined, false, true);

    return house;
  }
```
4. cloneHouse Function:
+ Clones instances of two different house types (detached_house and semi_house) with varying positions, rotations, and scales.
```typescript
const detached_house = createHouse(scene, 1, 0,0,0,); //.clone("clonedHouse");
    const detached_houseAggregate = new PhysicsAggregate(detached_house, PhysicsShapeType.BOX, { mass: 0 }, scene);
```
+ Defines an array (places) that specifies the type, rotation, and position for each house instance.
```typescript
//each entry is an array [house type, rotation, x, z]
    const places: number[] [] = []; 
    places.push([1, -Math.PI / 16, -6.8, 2.5 ]);
    places.push([2, -Math.PI / 16, -4.5, 3 ]);
```
+ Creates instances of houses based on the provided specifications.
+ Returns an array of created house instances.


5. Creating Instances:
+ Calls the cloneHouse function and assigns the result to that.house. 
+ Calls the createSphere function (which seems to be missing from the provided code) and assigns the result to that.box.
```typescript
for (let i = 0; i < places.length; i++) {
      if (places[i][0] === 1) {
          houses[i] = detached_house.createInstance("house" + i);
         // const detached_houseAggregate = new PhysicsAggregate(detached_house, PhysicsShapeType.BOX, { mass: 1 }, scene);
          
      }
      else {
          houses[i] = semi_house.createInstance("house" + i);
          //const semi_houseAggregate = new PhysicsAggregate(semi_house, PhysicsShapeType.BOX, { mass: 1 }, scene);
      }
        houses[i].rotation.y = places[i][1];
        houses[i].position.x = places[i][2];
        houses[i].position.z = places[i][3];
        const housesAggregate = new PhysicsAggregate(houses[i], PhysicsShapeType.BOX, { mass: 0 }, scene);
        
    }
  ```

















#### "actionManager" Function
1. Function Definition\
Defines the actionManager function that takes a Babylon.js scene as a parameter.
```typescript
function actionManager(scene: Scene) {
```

2. Action Manager Setup\
Initializes a new action manager for the scene.
```typescript
scene.actionManager = new ActionManager(scene);
```

3. Key Down and Key Up Event Handling\
Registers actions for key down and key up events, updating the keyDownMap array accordingly.

4. Return Statement\
Returns the created action manager.
```typescript
return scene.actionManager;
```


This code is using the Babylon.js framework to integrate the Havok Physics engine for handling physics simulations in a 3D scene. 

1. Importing Modules\
The code imports necessary modules from the Babylon.js framework and the Havok Physics engine. This includes the "HavokPhysics" object and specific classes and types related to physics.
```typescript
import HavokPhysics from "@babylonjs/havok";
import { HavokPlugin, PhysicsAggregate, PhysicsShapeType } from "@babylonjs/core";
```
2. Initializing Havok Physics\
This part of the code initializes Havok Physics asynchronously. The "HavokPhysics" function is called, which returns a promise. When the promise resolves, the "then" callback is executed, and the "havok" object is assigned to the "initializedHavok" variable.
```typescript
let initializedHavok;
HavokPhysics().then((havok) => {
  initializedHavok = havok;
});
```
3. Creating Havok Plugin\
Here, another instance of Havok Physics is created using "HavokPhysics()", and a new "HavokPlugin" is instantiated with "true" as the first argument (enabling debugging features) and the obtained "havokInstance".
```typescript
const havokInstance = await HavokPhysics();
const havokPlugin = new HavokPlugin(true, havokInstance);
```
4. Setting up Physics in the Scene\
This section sets up physics in the Babylon.js scene. It assigns the Havok Physics instance to "globalThis.HK" (globally accessible), and then the "enablePhysics" method of the scene is called. It takes a gravity vector ("new Vector3(0, -9.8, 0)") and the "havokPlugin" as parameters to enable physics in the scene.
```typescript
globalThis.HK = await HavokPhysics();
that.scene.enablePhysics(new Vector3(0, -9.8, 0), havokPlugin);
```


1. Material and Texture:\
A standard material (groundMat) is created. A diffuse texture is assigned to the material, using an image of a village green from the given URL.
2. Alpha Channel for Transparency:\
The hasAlpha property of the material's diffuse texture is set to true, indicating the presence of an alpha channel for transparency.
```typescript
 groundMat.diffuseTexture.hasAlpha = true;
 ```
3. Creating Ground Mesh:\
The MeshBuilder.CreateGround method is used to create a ground mesh with specific parameters such as height, width, and subdivisions.
4. Physics Properties:\
A PhysicsAggregate is created for the ground, specifying it as a box shape with a mass of 0. This implies that the ground is static and won't move due to physics simulation.
```typescript
 const groundAggregate = new PhysicsAggregate(ground, PhysicsShapeType.BOX, { mass: 0 }, scene);
  ```
5. Assigning Material:\
The created material (groundMat) is assigned to the ground mesh (ground).
6. Enabling Shadow Reception:\
The ground mesh is configured to receive shadows by setting the receiveShadows property to true.
```typescript
 ground.receiveShadows = true;
   ```
7. Return the Ground Mesh:\
The function returns the created ground mesh. \
The resulting effect is a textured ground with transparency and the ability to receive shadows in a Babylon.js scene.




### Code 11 - Lighting\

This code defines a TypeScript function createHemiLight that creates a hemispheric light in a Babylon.js scene. The hemispheric light simulates the effect of ambient light by emitting light in all directions from a specified direction.

1. Creating Hemispheric Light:
The function uses the HemisphericLight constructor to create a hemispheric light named "light." The light is oriented with a direction vector of (0, 1, 0), indicating that it emits light from the positive y-axis (upwards), simulating a light source from above.
```typescript
  // Create a hemispheric light named "light" with a direction of (0, 1, 0)
  const light = new HemisphericLight("light", new Vector3(0, 1, 0), scene);
  ```
2. Setting Intensity:
The intensity property of the hemispheric light is set to 0.8. This controls the brightness or strength of the light.
```typescript
  // Set the intensity of the hemispheric light to 0.8
  light.intensity = 0.8;
```
3. Return the Hemispheric Light:
The function returns the created hemispheric light.

4. Creating a Hemispheric Light Instance:
Finally, an instance of the hemispheric light is created in the Babylon.js scene using the createHemiLight function, and it is assigned to that.hemisphericLight.

This hemispheric light can be used to illuminate the scene with ambient light, providing a soft, uniform illumination from above. Adjusting the intensity parameter allows for fine-tuning the overall brightness of the scene.

### Code 12 - Camera\ 
This code defines a function createArcRotateCamera that creates and configures an ArcRotateCamera in a scene. The ArcRotateCamera is a type of camera that revolves around a target point while looking at that point, allowing for interactive navigation.

1. Initial Camera Parameters:\
Initial values for camAlpha, camBeta, camDist, and camTarget are defined. These values determine the initial state of the ArcRotateCamera.

2. Creating ArcRotateCamera:\
The ArcRotateCamera constructor is used to create a camera named "camera1" with the specified parameters:\
+ camAlpha: Horizontal rotation angle in radians (initially set to -π/2).\
+ camBeta: Vertical rotation angle in radians (initially set to π/2.5).\
+ camDist: Distance from the target point (initially set to 10).\
+ camTarget: The target point in 3D space (initially set to (0, 0, 0)).\
+ scene: The scene to which the camera belongs.

3. Attaching Controls:\
The attachControl method is called with true as an argument, enabling user controls for mouse/touch events. This allows users to interactively rotate, zoom, and pan the camera using input devices.

4. Return the ArcRotateCamera:\
+ The function returns the created ArcRotateCamera.

5. Creating a Camera Instance:\
+ An instance of the ArcRotateCamera is created by calling the createArcRotateCamera function in the scene.

This ArcRotateCamera is ready for use in a 3D scene, and users can control its movement and orientation interactively. The initial parameters (camAlpha, camBeta, camDist, camTarget) determine the camera's initial position and orientation. Adjusting these values can customize the camera's starting configuration.
```typescript
function createArcRotateCamera(scene: Scene) {
  // Initial camera parameters
  let camAlpha = -Math.PI / 2;
  let camBeta = Math.PI / 2.5;
  let camDist = 10;
  let camTarget = new Vector3(0, 0, 0);

  // Create an ArcRotateCamera named "camera1" with specified parameters
  let camera = new ArcRotateCamera(
    "camera1",
    camAlpha,
    camBeta,
    camDist,
    camTarget,
    scene,
  );

  // Enable user controls to attach mouse/touch events for camera interaction
  camera.attachControl(true);

  // Return the created ArcRotateCamera
  return camera;
}

// Create an ArcRotateCamera in the scene and assign it to a variable
let cameraInstance = createArcRotateCamera(scene);

```


This code defines a series of functions to create and manipulate 3D house meshes in a scene. Let's break down each part of the code:

1. createBox Function:
+ Creates a box mesh with a specified width.
+ Sets different textures for the box material based on the width.
+ Defines face UV coordinates to map textures to different sides of the box.
+ Returns the created box.

```typescript

const faceUV = [];
    if (width == 2) {
        faceUV[0] = new Vector4(0.6, 0.0, 1.0, 1.0); //rear face
        faceUV[1] = new Vector4(0.0, 0.0, 0.4, 1.0); //front face
        faceUV[2] = new Vector4(0.4, 0, 0.6, 1.0); //right side
        faceUV[3] = new Vector4(0.4, 0, 0.6, 1.0); //left side
    }
    else {
        faceUV[0] = new Vector4(0.5, 0.0, 0.75, 1.0); //rear face
        faceUV[1] = new Vector4(0.0, 0.0, 0.25, 1.0); //front face
        faceUV[2] = new Vector4(0.25, 0, 0.5, 1.0); //right side
        faceUV[3] = new Vector4(0.75, 0, 1.0, 1.0); //left side
    }
```
2. createRoof Function:
+ Creates a cylinder mesh to represent a roof.
+ Assigns a roof texture to the material.
+ Adjusts scaling, rotation, and position properties.
+ Returns the created roof.
```typescript
  function createRoof(scene: Scene, width: number) {
    const roofMat = new StandardMaterial("roofMat");
    roofMat.diffuseTexture = new Texture("https://assets.babylonjs.com/environments/roof.jpg");

    const roof = MeshBuilder.CreateCylinder("roof", {diameter: 1.3, height: 1.2, tessellation: 3});
    roof.material = roofMat;
    roof.scaling.x = 0.75;
    roof.scaling.y = width;
    roof.rotation.z = Math.PI / 2;
    roof.position.y = 1.22;
    
    return roof;
  }
```
3. createHouse Function:
+ Combines a box and a roof into a single mesh using Mesh.MergeMeshes.
+ Sets the position and rotation of the house.
+ Returns the created house.
```typescript
 function createHouse(scene: Scene, width: number) {
    const box = createBox(scene, width);
    const roof = createRoof(scene, width);
    const house: any = Mesh.MergeMeshes([box, roof], true, false, undefined, false, true);

    return house;
  }
```
4. cloneHouse Function:
+ Clones instances of two different house types (detached_house and semi_house) with varying positions, rotations, and scales.
```typescript
const detached_house = createHouse(scene, 1, 0,0,0,); //.clone("clonedHouse");
    const detached_houseAggregate = new PhysicsAggregate(detached_house, PhysicsShapeType.BOX, { mass: 0 }, scene);
```
+ Defines an array (places) that specifies the type, rotation, and position for each house instance.
```typescript
//each entry is an array [house type, rotation, x, z]
    const places: number[] [] = []; 
    places.push([1, -Math.PI / 16, -6.8, 2.5 ]);
    places.push([2, -Math.PI / 16, -4.5, 3 ]);
```
+ Creates instances of houses based on the provided specifications.
+ Returns an array of created house instances.


5. Creating Instances:
+ Calls the cloneHouse function and assigns the result to that.house. 
+ Calls the createSphere function (which seems to be missing from the provided code) and assigns the result to that.box.
```typescript
for (let i = 0; i < places.length; i++) {
      if (places[i][0] === 1) {
          houses[i] = detached_house.createInstance("house" + i);
         // const detached_houseAggregate = new PhysicsAggregate(detached_house, PhysicsShapeType.BOX, { mass: 1 }, scene);
          
      }
      else {
          houses[i] = semi_house.createInstance("house" + i);
          //const semi_houseAggregate = new PhysicsAggregate(semi_house, PhysicsShapeType.BOX, { mass: 1 }, scene);
      }
        houses[i].rotation.y = places[i][1];
        houses[i].position.x = places[i][2];
        houses[i].position.z = places[i][3];
        const housesAggregate = new PhysicsAggregate(houses[i], PhysicsShapeType.BOX, { mass: 0 }, scene);
        
    }
  ```

Physics aggregates are created for each house and box, but they are currently commented out (//). 

The that.house variable represents an array of house instances, and the that.box variable represents a single box instance.

To outline, this code is for creating 3D house  meshes in a scene with various textures, sizes, positions, and rotations. It also shows how to use of physics aggregates for potential physics simulations.

# Assets and texture sources
Player model has been sourced from babylon documentation website and can be found here:
* https://playground.babylonjs.com/#C38BUD#1

Village ground has been sourced from babylon documentation website and can be found here:
* https://assets.babylonjs.com/environments/villagegreen.png

Terrain that surrounds village has been sourced from babylon documentation and can be found here: 
* https://assets.babylonjs.com/environments/villageheightmap.png

Textures for the houses have been sourced from babylon documentation and can be found here: 
* https://assets.babylonjs.com/environments/roof.jpg
* https://assets.babylonjs.com/environments/semihouse.png
* https://assets.babylonjs.com/environments/cubehouse.png

# Import List (top section)
```typescript
//-----------------------------------------------------
//TOP OF CODE - IMPORTING BABYLONJS
import "@babylonjs/core/Debug/debugLayer";
import "@babylonjs/inspector";
import {
  Scene,
  ArcRotateCamera,
  Vector3,
  Vector4,
  HemisphericLight,
  SpotLight,
  MeshBuilder,
  Mesh,
  Light,
  Camera,
  Engine,
  StandardMaterial,
  Texture,
  Color3,
  Space,
  ShadowGenerator,
  PointLight,
  DirectionalLight,
  CubeTexture,
  Sprite,
  SpriteManager,
  SceneLoader,
  ActionManager,
  ExecuteCodeAction,
  AnimationPropertiesOverride,
  PhysicsImpostor,
  Sound,
  } from "@babylonjs/core";
  import * as GUI from "@babylonjs/gui";
  import HavokPhysics from "@babylonjs/havok";
  import { HavokPlugin, PhysicsAggregate, PhysicsShapeType } from "@babylonjs/core";
  //----------------------------------------------------
  function createSceneButton(scene: Scene, name: string, index: string, x: string, y: string, advtex) {
    let button = GUI.Button.CreateSimpleButton(name, index);
    button.left = x;
    button.top = y;
    button.width = "160px";
    button.height = "60px";
    button.color = "white";
    button.cornerRadius = 20;
    button.background = "green";

    const buttonClick = new Sound("MenuClickSFX", "./audio/menu-click.wav", scene, null, {
        loop: false,
        autoplay: false,
    });

    button.onPointerUpObservable.add(function () {
        console.log("THE GAME HAS STARTED");
        buttonClick.play();

        
        button.isVisible = false;
    });

    advtex.addControl(button);
    return button;
}

  //----------------------------------------------------
  //Initialisation of Physics (Havok)
  let initializedHavok;
  HavokPhysics().then((havok) => {
    initializedHavok = havok;
  });

  const havokInstance = await HavokPhysics();
  const havokPlugin = new HavokPlugin(true, havokInstance);

  globalThis.HK = await HavokPhysics();
  //----------------------------------------------------
  
  //----------------------------------------------------
  let keyDownMap: any[] = [];
  let currentSpeed: number = 0.1;
  let walkingSpeed: number = 0.1;
  let runningSpeed: number = 0.4;

  function importPlayerMesh(scene: Scene, collider: Mesh, x: number, y: number) {
    let tempItem = { flag: false } 
    let item: any = SceneLoader.ImportMesh("", "./models/", "dummy3.babylon", scene, function(newMeshes, particleSystems, skeletons) {
      let mesh = newMeshes[0];
      let skeleton = skeletons[0];
      skeleton.animationPropertiesOverride = new AnimationPropertiesOverride();
      skeleton.animationPropertiesOverride.enableBlending = true;
      skeleton.animationPropertiesOverride.blendingSpeed = 0.05;
      skeleton.animationPropertiesOverride.loopMode = 1; 

      let walkRange: any = skeleton.getAnimationRange("YBot_Walk");

      let animating: boolean = false;

      scene.onBeforeRenderObservable.add(()=> {
        let keydown: boolean = false;
        let shiftdown: boolean = false;
        if (keyDownMap["w"] || keyDownMap["ArrowUp"]) {
          mesh.position.z += 0.1;
          mesh.rotation.y = 0;
          keydown = true;
        }
        if (keyDownMap["a"] || keyDownMap["ArrowLeft"]) {
          mesh.position.x -= 0.1;
          mesh.rotation.y = 3 * Math.PI / 2;
          keydown = true;
        }
        if (keyDownMap["s"] || keyDownMap["ArrowDown"]) {
          mesh.position.z -= 0.1;
          mesh.rotation.y = 2 * Math.PI / 2;
          keydown = true;
        }
        if (keyDownMap["d"] || keyDownMap["ArrowRight"]) {
          mesh.position.x += 0.1;
          mesh.rotation.y = Math.PI / 2;
          keydown = true;
        }
        if (keyDownMap["Shift"] || keyDownMap["LeftShift"]) {
          currentSpeed = runningSpeed;
          shiftdown = true;
        } else {
          currentSpeed = walkingSpeed;
          shiftdown = false;
        }

        if (keydown) {
          if (!animating) {
            animating = true;
            scene.beginAnimation(skeleton, walkRange.from, walkRange.to, true);
          }
        } else {
          animating = false;
          scene.stopAnimation(skeleton);
        }

        //collision
        if (mesh.intersectsMesh(collider)) {
          console.log("COLLIDED");
        }
      });

      //physics collision
      item = mesh;
      let playerAggregate = new PhysicsAggregate(item, PhysicsShapeType.CAPSULE, { mass: 0 }, scene);
      playerAggregate.body.disablePreStep = false;

    });
    return item;
  }

  function actionManager(scene: Scene){
    scene.actionManager = new ActionManager(scene);

    scene.actionManager.registerAction(
      new ExecuteCodeAction(
        {
          trigger: ActionManager.OnKeyDownTrigger,
          //parameters: 'w'
        },
        function(evt) {keyDownMap[evt.sourceEvent.key] = true; }
      )
    );
    scene.actionManager.registerAction(
      new ExecuteCodeAction(
        {
          trigger: ActionManager.OnKeyUpTrigger
        },
        function(evt) {keyDownMap[evt.sourceEvent.key] = false; }
      )
    );
    return scene.actionManager;
  } 

  function createTorus(scene: Scene, px: number, py: number, pz: number) {
    const torus = MeshBuilder.CreateTorus("torus", {});
    torus.position = new Vector3(px, py, pz);
    scene.registerAfterRender(function () {
      torus.rotate(new Vector3(4, 8, 2)/*axis*/, 0.02/*angle*/, Space.LOCAL);
    });
    return torus;
  }

  function cloneTorus(scene: Scene): Mesh[] {
    const torusInstances: Mesh[] = [];
    const positions = [
      new Vector3(3, 0.5, 5),
      new Vector3(-3, 0.5, 1),
      new Vector3(0, 0.5, 0),
      new Vector3(3, 0.5, -3),
      new Vector3(-3, 0.5, -3),
      new Vector3(6, 0.5, 0),
      new Vector3(-6, 0.5, 0),
      new Vector3(0, 0.5, 6),
      new Vector3(0, 0.5, -6),
    ];
  
    for (let i = 0; i < 9; i++) {
      const position = positions[i];
  
      const torus = MeshBuilder.CreateTorus(`torus${i}`, {});
      torus.position = position;
  
      scene.registerAfterRender(function () {
        torus.rotate(new Vector3(4, 8, 2), 0.02, Space.LOCAL);
      });
  
      torusInstances.push(torus);
    }
  
    return torusInstances;
  }


  function checkCollision(cube: Mesh, torus: Mesh) {
    if (cube.intersectsMesh(torus)) {
      cube.dispose(); // Remove the cube from the scene
    }
  }

 // Add a variable to keep track of the score
 let score = 0;

 // Function to update the score and log it to the console
 function updateScore() {
     score++;
     if (score > 9) {
         score = 9; // Limit the score to a maximum of 9
     }
     console.log("Score: " + score);
 }
// Modify the createCube function to update the score on collision
function createCube(scene: Scene, x: number, y: number, z: number, toruses: Mesh[]) {
    let box: Mesh = MeshBuilder.CreateBox("box", {});
    box.position.x = x;
    box.position.y = y + 0.5; // Adjust the initial y position to place it just above the ground
    box.position.z = z;

    // Add physics properties to the cube
    const cubePhysicsOptions = {
        mass: 1,                 // Mass of the cube
        friction: 0.5,           // Friction coefficient
        restitution: 0.1         // Restitution (bounciness) coefficient
    };

    const boxAggregate = new PhysicsAggregate(box, PhysicsShapeType.BOX, cubePhysicsOptions, scene);

    // Register a function to check for collision with the torus meshes
    scene.registerBeforeRender(() => {
        for (const torus of toruses) {
            if (box.intersectsMesh(torus)) {
                // Remove the torus from the scene
                torus.dispose();
                // Update the score
                updateScore();
            }
        }
    });

    return box;
}


  //Create Terrain
  function createTerrain(scene: Scene) {
    //Create large ground for valley environment
    const largeGroundMat = new StandardMaterial("largeGroundMat");
    largeGroundMat.diffuseTexture = new Texture("https://assets.babylonjs.com/environments/valleygrass.png");
    
    const largeGround = MeshBuilder.CreateGroundFromHeightMap("largeGround", "https://assets.babylonjs.com/environments/villageheightmap.png", {width:150, height:150, subdivisions: 20, minHeight:0, maxHeight: 10});
    largeGround.material = largeGroundMat;
    return largeGround;
  }

  //Create more detailed ground
  function createGround(scene: Scene) {
    //Create Village ground

    const ground: Mesh = MeshBuilder.CreateGround("ground", {width:24, height:24});
    const groundAggregate = new PhysicsAggregate(ground, PhysicsShapeType.BOX, { mass: 0 }, scene);
    const groundMat = new StandardMaterial("groundMat");
    groundMat.diffuseTexture = new Texture("https://assets.babylonjs.com/environments/villagegreen.png");
    groundMat.diffuseTexture.hasAlpha = true;

   
    ground.material = groundMat;
    ground.position.y = 0.01;
    return ground;
  }

  //Create Skybox
  function createSkybox(scene: Scene) {
    //Skybox
    const skybox = MeshBuilder.CreateBox("skyBox", {size:150}, scene);
	  const skyboxMaterial = new StandardMaterial("skyBox", scene);
	  skyboxMaterial.backFaceCulling = false;
	  skyboxMaterial.reflectionTexture = new CubeTexture("textures/skybox", scene);
	  skyboxMaterial.reflectionTexture.coordinatesMode = Texture.SKYBOX_MODE;
	  skyboxMaterial.diffuseColor = new Color3(0, 0, 0);
	  skyboxMaterial.specularColor = new Color3(0, 0, 0);
	  skybox.material = skyboxMaterial;
    return skybox;
  }

  //Creating sprite trees
  function createTrees(scene: Scene) {
    const spriteManagerTrees = new SpriteManager("treesManager", "textures/palmtree.png", 2000, {width: 512, height: 1024}, scene);

    //We create trees at random positions
    for (let i = 0; i < 500; i++) {
        const tree = new Sprite("tree", spriteManagerTrees);
        tree.position.x = Math.random() * (-30);
        tree.position.z = Math.random() * 20 + 8;
        tree.position.y = 0.5;
    }

    for (let i = 0; i < 500; i++) {
        const tree = new Sprite("tree", spriteManagerTrees);
        tree.position.x = Math.random() * (25) + 7;
        tree.position.z = Math.random() * -35  + 8;
        tree.position.y = 0.5;
    }
    return spriteManagerTrees;
  }

  function createBox(scene: Scene, width: number) {
    const boxMat = new StandardMaterial("boxMat");
    if (width == 2) {
      boxMat.diffuseTexture = new Texture("https://assets.babylonjs.com/environments/semihouse.png") 
    }
    else {
       boxMat.diffuseTexture = new Texture("https://assets.babylonjs.com/environments/cubehouse.png");   
    }

    const faceUV: Vector4[] = [];
    if (width == 2) {
      faceUV[0] = new Vector4(0.6, 0.0, 1.0, 1.0); //rear face
      faceUV[1] = new Vector4(0.0, 0.0, 0.4, 1.0); //front face
      faceUV[2] = new Vector4(0.4, 0, 0.6, 1.0); //right side
      faceUV[3] = new Vector4(0.4, 0, 0.6, 1.0); //left side
    }
    else {
      faceUV[0] = new Vector4(0.5, 0.0, 0.75, 1.0); //rear face
      faceUV[1] = new Vector4(0.0, 0.0, 0.25, 1.0); //front face
      faceUV[2] = new Vector4(0.25, 0, 0.5, 1.0); //right side
      faceUV[3] = new Vector4(0.75, 0, 1.0, 1.0); //left side
    }

    const box = MeshBuilder.CreateBox("box", {faceUV: faceUV, wrap: true});
    box.position.y = 0.5;
    box.material = boxMat;
    return box;
  }

  function createRoof(scene: Scene, width: number) {
    const roofMat = new StandardMaterial("roofMat");
    roofMat.diffuseTexture = new Texture("https://assets.babylonjs.com/environments/roof.jpg");

    const roof = MeshBuilder.CreateCylinder("roof", {diameter: 1.3, height: 1.2, tessellation: 3});
    roof.material = roofMat;
    roof.scaling.x = 0.75;
    roof.scaling.y = width;
    roof.rotation.z = Math.PI / 2;
    roof.position.y = 1.22;
    return roof;
  }

  function createHouse(scene: Scene, width: number) {
    const box = createBox(scene, width);
    const roof = createRoof(scene, width);
    const house: any = Mesh.MergeMeshes([box, roof], true, false, undefined, false, true);

    return house;
  }

  //cloning function
  function cloneHouse(scene: Scene) {
    const detached_house = createHouse(scene, 1); //.clone("clonedHouse");
    detached_house.rotation.y = -Math.PI / 16;
    detached_house.position.x = -6.8;
    detached_house.position.z = 2.5;

    const semi_house = createHouse(scene, 2); //.clone("clonedHouse");
    semi_house.rotation.y = -Math.PI / 16;
    semi_house.position.x = -4.5;
    semi_house.position.z = 3;

    //each entry is an array [house type, rotation, x, z]
    const places: number[] [] = []; 
    places.push([1, -Math.PI / 16, -6.8, 2.5 ]);
    places.push([2, -Math.PI / 16, -4.5, 3 ]);
    places.push([2, -Math.PI / 16, -1.5, 4 ]);
    places.push([2, -Math.PI / 3, 1.5, 6 ]);
    places.push([2, 15 * Math.PI / 16, -6.4, -1.5 ]);
    places.push([1, 15 * Math.PI / 16, -4.1, -1 ]);
    places.push([2, 15 * Math.PI / 16, -2.1, -0.5 ]);
    places.push([1, 5 * Math.PI / 4, 0, -1 ]);
    places.push([1, Math.PI + Math.PI / 2.5, 0.5, -3 ]);
    places.push([2, Math.PI + Math.PI / 2.1, 0.75, -5 ]);
    places.push([1, Math.PI + Math.PI / 2.25, 0.75, -7 ]);
    places.push([2, Math.PI / 1.9, 4.75, -1 ]);
    places.push([1, Math.PI / 1.95, 4.5, -3 ]);
    places.push([2, Math.PI / 1.9, 4.75, -5 ]);
    places.push([1, Math.PI / 1.9, 4.75, -7 ]);
    places.push([2, -Math.PI / 3, 5.25, 2 ]);
    places.push([1, -Math.PI / 3, 6, 4 ]);

    const houses: Mesh[] = [];
    for (let i = 0; i < places.length; i++) {
      if (places[i][0] === 1) {
          houses[i] = detached_house.createInstance("house" + i);
      }
      else {
          houses[i] = semi_house.createInstance("house" + i);
      }
        houses[i].rotation.y = places[i][1];
        houses[i].position.x = places[i][2];
        houses[i].position.z = places[i][3];
    }

    return houses;
  }

  //----------------------------------------------------------------------------------------------
  function createHemiLight(scene: Scene) {
    const light = new HemisphericLight("light", new Vector3(0, 1, 0), scene);
    light.intensity = 0.8;
    return light;
  }

  
  function createArcRotateCamera(scene: Scene) {
    let camAlpha = -Math.PI / 2,
      camBeta = Math.PI / 2.5,
      camDist = 10,
      camTarget = new Vector3(0, 0, 0);
    let camera = new ArcRotateCamera(
      "camera1",
      camAlpha,
      camBeta,
      camDist,
      camTarget,
      scene,
    );
    camera.attachControl(true);
    return camera;
  }
  //----------------------------------------------------------
  
  //----------------------------------------------------------
  //BOTTOM OF CODE - MAIN RENDERING AREA FOR YOUR SCENE
export default function createStartScene(engine: Engine) {
  interface SceneData {
    scene: Scene;
    terrain?: Mesh;
    ground?: Mesh;
    skybox?: Mesh;
    trees?: SpriteManager;
    box?: Mesh;
    torus?: Mesh[];
    importMesh?: any;
    actionManager?: any;
    house?: any;
    light?: Light;
    hemisphericLight?: HemisphericLight;
    camera?: Camera;
  }

  let that: SceneData = { scene: new Scene(engine) };
  that.scene.debugLayer.show();
  that.scene.enablePhysics(new Vector3(0, -9.8, 0), havokPlugin);

  // Create the torus instances
  that.torus = cloneTorus(that.scene);

  // code instances
  that.terrain = createTerrain(that.scene);
  that.ground = createGround(that.scene);
  that.skybox = createSkybox(that.scene);
  that.trees = createTrees(that.scene);
  that.box = createCube(that.scene, 2, 2, 2, that.torus);
  that.importMesh = importPlayerMesh(that.scene, that.terrain, 0, 0);
  that.actionManager = actionManager(that.scene);

  // Housing
  that.house = cloneHouse(that.scene);

  // button
  let advancedTexture = GUI.AdvancedDynamicTexture.CreateFullscreenUI("myUI", true);
  let button1 = createSceneButton(that.scene, "but1", "Start Game", "0px", "-75px", advancedTexture);


  // Scene Lighting & Camera
  that.hemisphericLight = createHemiLight(that.scene);
  that.camera = createArcRotateCamera(that.scene);

  // Main render loop
  engine.runRenderLoop(() => {
    //rendering logic 
    that.scene.render();
  });

  return that;
}
//----------------------------------------------------
