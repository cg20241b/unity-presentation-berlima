[![Open in Codespaces](https://classroom.github.com/assets/launch-codespace-2972f46106e565e64193e422d61a12cf1da4916b45550586e14ef0a7c637dd04.svg)](https://classroom.github.com/open-in-codespaces?assignment_repo_id=16523274)

# Unity VFX Graph - Lightning Shader Effect Tutorial


## Installation Package
Firstly, install the packages we need in the package manager, which is `Shader Graph` and `Visual Effect Graph`<br>
<img width="1680" alt="Screenshot 2024-10-27 at 10 38 31 PM" src="https://github.com/user-attachments/assets/8018d58c-4e15-47cd-bd25-a3a72d73ab72">

## VFX Shader Graph
VFX Shader graph here is used for designing the look of the lightning itself (like the brightness, shape, and how it flickers or fades).

<img width="1680" alt="Screenshot 2024-10-27 at 10 47 19 PM" src="https://github.com/user-attachments/assets/b9f32952-bab3-46c0-9893-03d3f3b4a29d">

Things that we build for the shader graph;
* - Time Node
- The `Time` node provides a variety of time-based values, including:
    - `Time`: A simple time variable, increasing as the game runs.
    - `Sine Time`: Outputs a sine wave based on time, oscillating between -1 and 1.
    - `Cosine Time`: Outputs a cosine wave with similar oscillation.
    - `Delta Time`: Represents the time between the current and previous frame 
    - This is used here to animate or influence other properties dynamically.

* - Multiply Node (Yellow Box)
- This node multiplies two values.
- It receives `Time` as input, which may serve to animate the UV offset or other properties continuously.
- The yellow color shown here is just a preview to signify an intermediate step.

* - Noise Speed & Noise Scale
- These nodes are parameters used for controlling the behavior of a noise texture.
- `NoiseSpeed`: Determines how fast the noise texture moves or changes over time.
- `NoiseScale`: Controls the scale or frequency of the noise texture.
- These values help create movement and variation in the noise pattern, which is essential for effects like flickering or randomness.

* - Tiling and Offset Node
- This node allows adjustments to the UV coordinates, including tiling (repetition) and offset (position).
- By linking it with time and other parameters, this node can animate texture scrolling or scaling, essential for making the lightning effect appear to move.

* - Simple Noise Node
- This node generates a noise texture based on the input UV coordinates.
- In this case, it’s likely set to `Deterministic` to ensure consistent results in the noise pattern.
- The noise is used to create random variations, adding texture and natural look to the lightning effect.

* -  Rectangle Node
- The `Rectangle` node generates a simple rectangle shape.
- The `Width` and `Height` parameters control the shape's size.
- The output here appears as a binary black-and-white texture, potentially representing the base shape of the lightning effect before adding noise.

* - Lerp Node
- This node linearly interpolates between two values (A and B) based on a T value.
- The `DistortionAmount` parameter connected here controls the intensity of this blend, possibly mixing between the original shape and a noisy version for a distorted lightning effect.
- By changing this parameter over time, the shader can add or reduce distortion, adding dynamic behavior.

* - Multiply Node (After Lerp)
- This `Multiply` node combines the output of the `Lerp` operation with the rectangle shape and noise texture.
- The multiplication here likely amplifies or masks parts of the lightning shape, adding depth or intensity to specific areas.

* - UV Node
- This node represents the UV mapping of the texture, defining how it’s applied on the model.
- It allows precise control over where the textures are placed and helps in adjusting offsets for animation or effects.

* - Fragment Node
- This node is the final output section:
    - `Base Color`: Sets the main color of the lightning.
    - `Alpha`: Controls transparency.
    - `Emission`: Defines how much light the material emits, which is crucial for a glowing effect like lightning.
    - By linking all the previous effects and settings here, the shader combines the shape, noise, color, and emission for a cohesive lightning effect.