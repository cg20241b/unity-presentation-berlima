[![Open in Codespaces](https://classroom.github.com/assets/launch-codespace-2972f46106e565e64193e422d61a12cf1da4916b45550586e14ef0a7c637dd04.svg)](https://classroom.github.com/open-in-codespaces?assignment_repo_id=16523274)

# Unity VFX Graph - Lightning Shader Effect Tutorial


## Installation Package
Firstly, install the packages we need in the package manager, which is `Shader Graph` and `Visual Effect Graph`<br>
<img width="1680" alt="Screenshot 2024-10-27 at 10 38 31 PM" src="https://github.com/user-attachments/assets/8018d58c-4e15-47cd-bd25-a3a72d73ab72">

## VFX Shader Graph
VFX Shader graph here is used for designing the look of the lightning itself (like the brightness, shape, and how it flickers or fades).

<img width="1680" alt="Screenshot 2024-10-27 at 10 47 19 PM" src="https://github.com/user-attachments/assets/b9f32952-bab3-46c0-9893-03d3f3b4a29d">

Things that we build for the shader graph;
1. Time Node
- The `Time` node provides a variety of time-based values, including:
    - `Time`: A simple time variable, increasing as the game runs.
    - `Sine Time`: Outputs a sine wave based on time, oscillating between -1 and 1.
    - `Cosine Time`: Outputs a cosine wave with similar oscillation.
    - `Delta Time`: Represents the time between the current and previous frame 
    - This is used here to animate or influence other properties dynamically.

2. Multiply Node (Yellow Box)
- This node multiplies two values.
- It receives `Time` as input, which may serve to animate the UV offset or other properties continuously.
- The yellow color shown here is just a preview to signify an intermediate step.

3. Noise Speed & Noise Scale
- These nodes are parameters used for controlling the behavior of a noise texture.
- `NoiseSpeed`: Determines how fast the noise texture moves or changes over time.
- `NoiseScale`: Controls the scale or frequency of the noise texture.
- These values help create movement and variation in the noise pattern, which is essential for effects like flickering or randomness.

4. Tiling and Offset Node
- This node allows adjustments to the UV coordinates, including tiling (repetition) and offset (position).
- By linking it with time and other parameters, this node can animate texture scrolling or scaling, essential for making the lightning effect appear to move.

5. Simple Noise Node
- This node generates a noise texture based on the input UV coordinates.
- In this case, it’s likely set to `Deterministic` to ensure consistent results in the noise pattern.
- The noise is used to create random variations, adding texture and natural look to the lightning effect.

6.  Rectangle Node
- The `Rectangle` node generates a simple rectangle shape.
- The `Width` and `Height` parameters control the shape's size.
- The output here appears as a binary black-and-white texture, potentially representing the base shape of the lightning effect before adding noise.

7. Lerp Node
- This node linearly interpolates between two values (A and B) based on a T value.
- The `DistortionAmount` parameter connected here controls the intensity of this blend, possibly mixing between the original shape and a noisy version for a distorted lightning effect.
- By changing this parameter over time, the shader can add or reduce distortion, adding dynamic behavior.

8.  Multiply Node (After Lerp)
- This `Multiply` node combines the output of the `Lerp` operation with the rectangle shape and noise texture.
- The multiplication here likely amplifies or masks parts of the lightning shape, adding depth or intensity to specific areas.

9.  UV Node
- This node represents the UV mapping of the texture, defining how it’s applied on the model.
- It allows precise control over where the textures are placed and helps in adjusting offsets for animation or effects.

10. Fragment Node
- This node is the final output section:
    - `Base Color`: Sets the main color of the lightning.
    - `Alpha`: Controls transparency.
    - `Emission`: Defines how much light the material emits, which is crucial for a glowing effect like lightning.
    - By linking all the previous effects and settings here, the shader combines the shape, noise, color, and emission for a cohesive lightning effect.

## Visual Effect Graph

<img width="1680" alt="Screenshot 2024-10-27 at 11 53 14 PM" src="https://github.com/user-attachments/assets/e4c76c4d-3d22-492c-9aae-59167e34765c">

**LIGHTNING**
* - Spawn System - Spawn
    1. Single Burst: The particles are emitted in a single burst, meaning all particles are released at once rather than continuously.
    2. Spawn Mode: Set to Constant, meaning the burst is triggered without any randomization or variation.
    3. Delay Mode: Also set to Constant, which means the delay before the burst is fixed. Here, it’s set to 0, so the burst happens instantly when triggered.
    4. Count: Set to 1, which likely refers to how many bursts occur per event (e.g., one single release of particles per trigger).
* - Initialize Particle - Initialize Particle
    1. Capacity: Set to 32, which limits the number of particles in this effect to 32. This helps manage performance and ensures that the effect doesn’t exceed the required particle count.
    2. Bounds Mode: Set to Recorded, which means that the particle system automatically determines the bounds of the effect. This can help with optimizing rendering, as only the necessary area is rendered.
    3. Bounds Padding: Set to 0.5 for each axis, which adds a small padding around the recorded bounds, ensuring particles remain visible even if they go slightly outside the original bounds.
    4. Set Lifetime: The Lifetime is set to 0.5, which means each particle will last for half a second before disappearing. This short lifetime contributes to the quick, flashing effect typical of lightning.
    5. Set Pivot.XYZ:
       Channels: Set to XYZ, indicating that the pivot can be modified on all three axes.
       Pivot: Here, the values are set to (0, 1, 0), meaning the pivot point is slightly offset on the Y-axis. This offset can help position the particles for a more natural lightning appearance.
* - Output Particle Mesh - Output Particle Mesh
    1. Shader Graph: Uses a shader called Lightning01 with an Alpha blend mode, which makes it transparent. This shader is likely tailored for lightning, adding glow or distortion effects.
    2. Color: The color is set to a bright blueish-white shade (R: 1.2264, G: 2.450, B: 4, A: 1), giving the lightning an electric, high-intensity color.
    3. Width and Height: Set to 2 each, defining the particle's dimensions. This size is likely determined based on the scale needed for the lightning effect.
    4. Noise Scale: Set to 25, which controls the level of noise applied to the particle's shape. This higher value likely adds a lot of turbulence to the particle, making the lightning appear jagged and random.
    5. Noise Speed: Set to 0.8 in the X-axis and 0.2 in the Y-axis, controlling the speed at which the noise is applied across each axis. The higher speed in the X direction could give the lightning a directional flow.
    6. Distortion Amount: Set to 0.2, which adds subtle distortion to the particles, creating a wavy, energetic look that complements the lightning effect.
    7. Mesh: Set to Plane, meaning each particle is rendered as a flat plane. This is a common choice for effects like lightning, as it allows for quick rendering and flexibility with transparency and shaders.
    8. Set Scale.XYZ:
       Channels: Set to XYZ, meaning the scale can be independently controlled on each axis.
       Sample Curve:
        Two Sample Curve nodes are connected to the Scale parameter, which are using Age Over Lifetime (0..1) on the X, Y, and Z channels. This adjusts the particle's scale as it ages, creating dynamic size changes over the particle's lifetime.

**FLASH**
* - Spawn System
    1. Single Burst: This option is selected, meaning the particles will spawn in a single burst rather than continuously.
    2. Spawn Mode (Constant): Spawning occurs instantly without randomization.
    3. Count (1): Only one particle is created per burst.
    4. Delay (0): The particle is spawned immediately when triggered.
* - Initialize Particle
    1. Capacity (32): The maximum number of particles that can be active at once is set to 32.
    2. Bounds Padding (0.5): This sets extra padding around the bounds of the particles, which helps prevent culling (removing from view) too early.
    3. Set Lifetime (0.2): Each particle has a short lifespan of 0.2 seconds, giving a quick flash-like effect.
* - Output Particle Quad
    1. Shader Graph: The shader graph for this effect is set to default, meaning the particle rendering uses a basic shader.
    2. Blend Mode (Additive): This blend mode makes the particles additive, which is useful for lightning or glowing effects as it enhances the brightness by adding colors together.
    3. Orient (Face Camera Plane): The particles are oriented to always face the camera, making them look consistent regardless of the viewer's perspective.
    4. Set Size over Life: This setting adjusts the particle size over its lifespan. A gradual decrease in size creates a dissipating effect as the particle disappears.
    5. Set Color: A color value is assigned with an HDR (high dynamic range) setting, which intensifies the brightness, adding to the lightning effect.
    6. Set Size (7): The particle starts with a size of 7 units, giving it a prominent appearance.

**GLOW**
* - Spawn System
    1. Single Burst: As with the flash effect, the particle is emitted as a single burst rather than continuously.
    2. Spawn Mode (Constant): Particles are spawned without random delays.
    3. Count (1): Only one particle is spawned.
    4. Delay (0): The particle spawns immediately when triggered.
* - Initialize Particle
    1. Capacity (32): The maximum number of active particles is limited to 32, allowing for multiple instances if needed.
    2. Bounds Padding (0.5): Adds padding around the particle bounds, preventing early culling.
    3. Set Lifetime (0.6): Each particle lasts 0.6 seconds, which is longer than the flash effect, creating a sustained glow.
* - Update Particle
    1. No additional behaviors are applied during the particle's lifetime here, keeping the effect simple.
* - Output Particle Quad
    1. Shader Graph: Uses a default shader.
    2. Blend Mode (Additive): The Additive blend mode is used again, enhancing the glow by making colors brighter when overlapped.
    3. Orient (Face Camera Plane): The particles face the camera, ensuring they look consistent from all angles.
    4. Set Size over Life: The size of the particle decreases gradually, which contributes to a "fading out" effect.
    5. Set Color: The color is set using a Vector4 with an HDR (high dynamic range) value, but it is multiplied by 0.5 using a "Multiply" node. This dims the intensity compared to the flash effect, producing a softer glow.
    6. Set Size (2): The particle size is smaller at 2 units, creating a more concentrated glow.

**IMPACT FLASH**
* - Spawn System
    1. Single Burst: The particle is emitted in a single burst when triggered, suitable for an instantaneous impact effect.
    2. Count (1): Only one particle is created per impact. 
    3. Delay (0): The particle spawns immediately without delay upon triggering.
* - Initialize Particle
    1. Capacity (32): The effect can support up to 32 active particles, which provides flexibility if the impact occurs multiple times.
    2. Bounds Padding (0.5): Adds a bit of extra space around the particle bounds to avoid early culling.
    3. Set Lifetime (0.2): The particle has a very short lifetime of 0.2 seconds, creating a brief flash typical of an impact.
    4. Set Position: This property is controlled by an "ImpactOffset" node, with the Y-position set to -1.2. This offset likely positions the flash slightly below a central point, simulating an effect on a surface or ground level.
* - Output Particle Quad
    1. Shader Graph: Uses a default shader.
    2. Blend Mode (Additive): The Additive blend mode intensifies the brightness by layering colors, which is ideal for a flash effect.
    3. Orient (Face Camera Plane): The particle always faces the camera, ensuring visibility from any angle.
    4. Set Size over Life: The particle size gradually decreases over its short lifespan, giving it a shrinking or dissipating effect.
    5. Set Color: The color is a bright HDR (high dynamic range) value, connected to "Color01," which enhances the visual intensity of the flash.
    6. Set Size (2): The initial particle size is set to 2 units, giving it a noticeable but not overpowering size for the flash.
    7. ImpactDelay: A delay node connected to the Spawn System suggests there may be a slight delay before the flash occurs, possibly to time it with the impact event.

**PARTICLES**
* - Spawn System
    1. Single Burst: This option is checked, meaning particles are emitted in a single burst rather than continuously. This is suitable for lightning, as it creates an instant flash or burst.
    2. Count: Set to 30, meaning 30 particles are spawned in each burst.
    3. Delay: With a delay of 0, the particles spawn immediately when triggered. This instant spawn aligns with the quick and sudden nature of lightning.
* - Initialize Particle
    1. Set Velocity Random: The particles have a randomized velocity in all three axes. The velocity is set to range between -2 and 2 for X and Z, and between 0.1 and 2 for Y. This spread in velocity makes the particles move in different directions, creating a chaotic, electric feel.
    2. Set Lifetime Random: The lifetime of particles is set randomly between 0.2 and 0.5 seconds, making them disappear quickly, which is typical of a lightning effect.
    3. Set Position: The Y position has an offset of -1.2, meaning the particles start slightly below the origin point, potentially giving the effect that the lightning strikes upwards.
* - Output Particle Quad
    1. Orient: Along Velocity: This makes each particle align along its velocity direction, causing them to stretch or trail as they move. This is a common technique to create streaking effects like lightning bolts.
    2. Set Size over Life: Particles scale over their lifetime, likely shrinking or fading out to enhance the burst and dissipate effect.
    3. Multiply Color over Life: Color is modified over the particle’s life, giving a fading or pulsing glow. It uses both color and alpha, so particles fade out, adding to the flash-like quality.
    4. Set Scale.XYZ Random: The random scaling (between 0.02 and 0.09 in X and Z, and 0.3 to 0.5 in Y) means each particle has slightly different dimensions, creating a more dynamic and less uniform look.
    5. Set Color: The color is set to a high-intensity (HDR) bluish tint, typical for lightning effects, with values in the blue and white range to make it vibrant.

**Impact Glow**
* - Spawn System
    1. Single Burst: This option is checked, meaning that the effect only occurs once per trigger, matching the idea of a single, instantaneous impact.
    2. Count: Set to 1, meaning only one particle is spawned. This keeps the effect simple and focused on creating a single glow rather than a spread of particles.
    3. Delay: Set to 0, so the particle spawns immediately when triggered, which is typical for an impact effect.
* - Initialize Particle
    1. Lifetime: Set to 0.6 seconds, giving the particle a short duration before it fades out, which works well for a flash effect that shouldn’t linger for long.
    2. Set Position: The Y-position is offset by -1.2 units, positioning the particle slightly below the origin. This could give the impression of a glow emanating from the ground or another object just below the impact point.
* - Output Particle Quad
    1. Orient: Face Camera Plane: This setting ensures that the particle always faces the camera, creating a flat, circular glow that looks consistent from any viewing angle.
    2. Set Size over Life: The size changes over the particle's lifetime, allowing it to start large and shrink, or vice versa. This can make the glow appear to expand and fade, enhancing the impact effect.
    3. Set Color: The color is a high-intensity, HDR bluish-white glow. This high brightness gives a vibrant, eye-catching effect that simulates an intense light burst.
    4. Set Size: Set to 2, which determines the initial size of the particle, making it fairly visible but not overly large.

Summary<br>
Lightning: Quick bursts with high emission and erratic motion.<br>
Flash: Brief, bright explosion of light on lightning initiation.<br>
Glow: Persistent, ambient light around lightning to enhance visual impact.<br>
Impact Flash: Intense flash at the impact point for realism.<br>
Particles: Spark-like particles for additional details and chaos.<br>
Impact Glow: Soft, fading glow to represent residual energy post-impact.<br>
