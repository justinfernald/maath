[![Discord Shield](https://img.shields.io/discord/740090768164651008?style=flat&colorA=000000&colorB=000000&label=&logo=discord&logoColor=ffffff)](https://discord.gg/poimandres)

<a href="https://github.com/pmndrs/maath"><img src="https://github.com/pmndrs/maath/blob/main/hero.svg?raw=true" /></a>
<br />

```bash
yarn add maath
```

This is a collection of useful math helpers, random generators, bits and bobs.

The library is mostly meant to be used with [three.js](https://github.com/mrdoob/three.js/), so if you are using it outside of a three project, make sure you check the source and - if you don't need the dep - just copy paste!

### Check out the demos on Codesandbox: 🪶

| <a href="https://codesandbox.io/s/github/pmndrs/maath/tree/main/demo/src/sandboxes/points"><img   src="https://codesandbox.io/api/v1/sandboxes/lex1g/screenshot.png"  /></a> | <a href="https://codesandbox.io/s/github/pmndrs/maath/tree/main/demo/src/sandboxes/convex-hull"><img src="https://codesandbox.io/api/v1/sandboxes/fh8l2/screenshot.png" /></a> | <a href="https://codesandbox.io/s/github/pmndrs/maath/tree/main/demo/src/sandboxes/circumcircle"><img src="https://codesandbox.io/api/v1/sandboxes/zuff9/screenshot.png"  /></a> |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

> 🟡 The library is still heavily WIP

### But why?

Yes, there are a lot of these libraries. The more the merrier! We are all here to learn, and maintaining a dedicated library for our creative endeavors at [Poimandres](https://github.com/pmndrs) just made sense.

## Contributing

Do you want to add something? No rules, but keep these in mind:

- try to add explainers to whatever function you add, if you saw it in a tweet link that!
- add a cool example! Make it a sandbox or whatever, just show how the function can be used creatively
- keep copy-paste simple. Try not to add too many inter-dependencies so the functions are copy-paste friendly
- loose typing. Try to add typing, but don't go crazy with generics and complexity

If you are not sure how to help, check out the [🟡 Roadmap](#-roadmap) below.

## 🪶 Reference

### Using specific entry points

```js
// you can import the namespaces from the main entrypoint
import { buffer, random } from "maath";
// or import each function or all of them from each namespace entrypoint
import * as buffer from "maath/buffer";
import { inSphere } from "maath/random";
```

### Buffer

```js
import * as buffer from "maath/buffer";
```

#### 🪶 toVectorArray(buffer, stride)

Converts an `[..., x, y, z, ...]` typed array to a `Vector[]`

```js
const myBuffer = new Float32Array(100 * 3);
const myArray = toVectorArray(myBuffer, 3);
```

#### 🪶 swizzleBuffer(buffer, axes)

[Swizzle](<https://en.wikipedia.org/wiki/Swizzling_(computer_graphics)>) the individual vectors in a vector buffer

```js
const myBuffer = new Float32Array(100 * 3);
myBuffer.push(0, 1, 2);

swizzleBuffer(myBuffer, "xzy"); // buffer is now [0, 2, 1]
```

This is a way to make simple rotations.

#### 🪶 addAxis(buffer, size, getZValue)

Adds a z axis to an `[..., x, y, ...]` typed array:

```js
const my2DBuffer = new Float32Array(100 * 2);
const my3DBuffer = addAxis(my2DBuffer, 2, () => Math.random()); // zAxis will now be a random value between 0 and 1
const my4DBuffer = addAxis(my3DBuffer, 3, () => 1); // 4th value (imagine a in rgba) will be 1
```

#### 🪶 lerpBuffers(bufferA, bufferB, destinationBuffer, t)

Linearly interpolate two buffers, writing on a third one.

```js
const mySphere = inSphere(new Float32Array(100 * 3), { radius: 4 });
const myBox = inBox(new Float32Array(100 * 3), { side: 4 });

const interpolationTarget = myBox.slice(0);

lerpBuffers(mySphere, myBox, interpolationTarget, Math.sin(performance.now()));
```

### Geometry

```js
import * as geometry from "maath/geometry";
```

#### 🪶 roundedPlaneGeometry(width = 1, height = 1, radius = 0.2, segments = 16)

```js
const geo = new RoundedPlaneGeometry();
const mesh = new THREE.Mesh(geo, material);
```

#### 🪶 applyBoxUV(bufferGeometry)

Applies box-projected UVs to a buffer geometry.

#### 🪶 applySphereUV(bufferGeometry)

Applies spherical UVs to a buffer geometry.

#### 🪶 applyCylindricalUV(bufferGeometry)

Applies cylindrical-projected UVs to a buffer geometry.

### Easing

```js
import * as easing from "maath/easing";
```

Unity-smooth-damping functions based on Game Programming Gems 4 Chapter 1.10. These are fast, refresh-rate independent, interruptible animation primitives primed to THREE.Vector2D, 3D, 4D, Euler (shortest path), Matrix4, Quaternion and Color.

```tsx
export function damp(
  /** The object */
  current: { [key: string]: any },
  /** The key to animate */
  prop: string,
  /** To target (or goal) value */
  target: number,
  /** Approximate time to reach the target. A smaller value will reach the target faster. */
  smoothTime = 0.25,
  /** Frame delta, for refreshrate independence */
  delta = 0.01,
  /** Optionally allows you to clamp the maximum speed. If smoothTime is 0.25s and looks OK
   *  going between two close points but not for points far apart as it'll move very rapid,
   *  then a maxSpeed of e.g. 1 which will clamp the speed to 1 unit per second, it may now
   *  take much longer than smoothTime to reach the target if it is far away. */
  maxSpeed = Infinity,
  /** Easing function */
  easing = (t: number) => 1 / (1 + t + 0.48 * t * t + 0.235 * t * t * t),
  /** End of animation precision */
  eps = 0.001
);
```

```jsx
import { damp, damp2, damp3, damp4, dampE, dampM, dampQ, dampS, dampC } from 'maath/easing'

function frameloop() {
  const delta = clock.getDelta()
  // Animates foo.bar to 10
  damp(foo, "bar", 10, 0.25, delta)

  // Animates mesh.position to 0,1,2
  damp3(mesh.position, [0, 1, 2], 0.25, delta)
  // Also takes vectors, shallow vectors and scalars
  // damp3(mesh.position, new THREE.Vector3(0, 1, 2), 0.25, delta)
  // damp3(mesh.position, { x: 0, y: 1, z: 2 }, 0.25, delta)
  // damp3(mesh.scale, 2, 0.25, delta)

  dampC(mesh.material.color, "green", 0.25, delta)
  // Also takes colors, fake colors, numbers and arrays
  // dampC(mesh.material.color, new THREE.Color("green"), 0.25, delta)
  // dampC(mesh.material.color, 0xdead00, 0.25, delta)
  // dampC(mesh.material.color, [1, 0, 0], 0.25, delta)
  // dampC(mesh.material.color, { r: 1, g: 0, b: 0 }, 0.25, delta)

  dampE(mesh.rotation, [Math.PI / 2, 0, 0], 0.25, delta)
  // Also takes eulers
  // dampE(mesh.rotation, new THREE.Euler(Math.PI / 2, 0, 0), 0.25, delta)

  // damp2 for Vector2
  // damp4 for Vector4
  // dampM for Matrix4
  // dampQ for Quaternion
  // dampS for Spherical
```

#### 🪶 easing functions

6 easing functions are available with in, out and inOut variants:

```jsx
import { sine, cubic, quint, circ, quart, expo } from "maath/easing";

sine.in(t);
sine.out(t);
sine.inOut(t);
```

3 specific functions:

```jsx
import { rsqw, exp, linear } from "maath/easing";

// rounded-square wave
rsqw(t, delta);
// unity smooth damping
exp(t);
// linear
linear(t); // === t
```

### Matrix

```js
import * as matrix from "maath/matrix";
```

#### 🪶 determinant2(...matrixInRowMajorOrder)

Returns the determinant of a passed 2x2 matrix:

```js
const d = determinant2(1, 1, 2, 2);
```

#### 🪶 determinant3(...matrixInRowMajorOrder)

Returns the determinant of a passed 3x3 matrix:

```js
const d = determinant3(1, 1, 1, 2, 2, 2);
```

#### 🪶 determinant4(...matrixInRowMajorOrder) // TBD

#### 🪶 getMinor(matrix, column, row)

Returns the [minor](<https://en.wikipedia.org/wiki/Minor_(linear_algebra)>) of a given matrix.

```js
const minor = getMinor([1, 2, 1, 2, 1, 1, 3, 2, 3], 1, 1);

// minor will be the determinant of the submatrix without row 1 and colum 1
// | 1 1 |
// | 2 3 |
```

### Misc

```tsx
// Clamps a value between a range.
clamp(value: number, min: number, max: number): number
// Loops the value t, so that it is never larger than length and never smaller than 0.
repeat(t: number, length: number): number
// Calculates the shortest difference between two given angles.
deltaAngle(current: number, target: number): number
// Converts degrees to radians.
degToRad(degrees: number): number
// Converts radians to degrees.
radToDeg(radians: number): number
// adapted from https://gist.github.com/stephanbogner/a5f50548a06bec723dcb0991dcbb0856 by https://twitter.com/st_phan
fibonacciOnSphere(buffer: TypedArray, { radius = 1 }): void
// Checks if vector a is equal to vector b, with tolerance
vectorEquals(a, b, eps = Number.EPSILON): boolean
/**
 * Sorts vectors in lexicographic order, works with both v2 and v3
 *
 *  Use as:
 *  const sorted = arrayOfVectors.sort(lexicographicOrder)
 */
// https://en.wikipedia.org/wiki/Lexicographic_order
lexicographic(a: Vector2 | Vector3, b: Vector2 | Vector3): number
/**
 * Convex Hull
 *
 * Returns an array of 2D Vectors representing the convex hull of a set of 2D Vectors
 */
convexHull(_points: Vector2[]): Vector2[]
// ...
remap(x: number, [low1, high1]: number[], [low2, high2]: number[])
```

### Random

```js
import * as random from "maath/random";
```

#### 🪶 onTorus(buffer, { innerRadius, outerRadius })

[TODO](https://math.stackexchange.com/questions/2017079/uniform-random-points-on-a-torus)

#### 🪶 inTorus(buffer, { innerRadius, outerRadius })

[TODO](https://answers.unity.com/questions/1259394/finding-random-position-in-torus.html)

### Triangle

```js
import * as triangle from "maath/triangle";
```

TBD

## Inspiration

The kitchen-sink nature of the library was inspired by other projects that manage to bring together an immense amount of knowledge from different domains that would otherwise be fragmented in many places or even lost:

- [drei](https://github.com/pmndrs/drei) 🌭 useful helpers for react-three-fiber
- [lygia](https://github.com/patriciogonzalezvivo/lygia) a granular and multi-language shader library designed for performance and flexibility

## 🟡 Roadmap

- Make the random generator seedable for every function
- Figure out a good API for vectors
- Figure out a good API for functions that might work on both buffers and arrays of vectors
- Fix type errors that might come from using different vector libs
- Keep adding tests
- Figure out if we can get rid of the Three.js dependency. While useful, it feels superfluous
