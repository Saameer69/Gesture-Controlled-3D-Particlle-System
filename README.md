# ✋ Gesture-Controlled 3D Particle System

A high-performance, real-time interactive 3D particle system built with Three.js and MediaPipe Hands. This project showcases the fusion of computer vision and WebGL, allowing users to dynamically control complex 3D graphics using only hand gestures captured via webcam.

## 🚀 Features

This application transforms the user's hand into a powerful 3D controller, enabling real-time manipulation of over 15,000 particles.

| Feature | Technology | Description |
| :--- | :--- | :--- |
| **Real-time Hand Tracking** | MediaPipe Hands | Uses a convolutional neural network to detect and track 21 hand landmarks (skeleton). |
| **Morphing Templates** | Custom GLSL Shaders | Seamlessly interpolates particle positions between complex shapes. |
| **Dynamic Expansion** | Pinch Gesture Control | The distance between the **Thumb (4)** and **Index Finger (8)** controls the particle explosion/contraction factor. |
| **3D Rotation** | Hand Position | Moving the wrist (landmark **0**) rotates the entire particle system in 3D space. |
| **Color Control** | Vertical Position (Y-axis) | The vertical height of the hand dynamically shifts the particle color palette (HSL Hue). |

## ✨ Particle Templates

The system can morph between six distinct, algorithmically generated shapes:

| Shape Name | Concept | Generator Function |
| :--- | :--- | :--- |
| **Sphere** | Standard 3D Volume | `getSpherePoints()` |
| **Heart** | Complex Parametric Curve | `getHeartPoints()` |
| **Flower** | Volumetric Petals | `getFlowerPoints()` |
| **Galaxy** | Archimedean Spiral | `getSpiralGalaxyPoints()` |
| **Fireworks Emitter** | Velocity-based burst pattern | `getFireworksEmitterPoints()` |
| **Logo 'S'** | 2D Geometry Extrusion | `getLogoPoints()` |

## ⚙️ Core Technology Stack

* **Three.js (r160+):** The primary WebGL library used for 3D rendering, scene management, and creating the particle geometry.
* **GLSL (Shader Language):** Utilized for high-performance Vertex and Fragment Shaders to execute the morphing, expansion, and color logic directly on the GPU.
* **MediaPipe Hands:** Google's open-source machine learning solution for robust hand and finger tracking.
* **HTML/CSS/JavaScript (ES6):** The core web platform technologies.

## 🛠️ Installation and Setup

Since this is a single HTML file, setup is straightforward.

1.  **Clone the Repository:**
    ```bash
    git clone [YOUR_REPO_URL]
    cd gesture-controlled-particle-system
    ```
2.  **Open in Browser:**
    * Open the `index.html` file directly in a modern web browser (Chrome recommended) by double-clicking it.
    * **Crucially,** the browser will prompt you to **Allow Camera Access**. You must accept this for the MediaPipe model to initialize and begin tracking.

## 💻 Technical Deep Dive (The Morphing Magic)

The ability to smoothly transition between two completely different 3D point clouds is achieved entirely in the GPU via a **Vertex Shader** using attribute interpolation:

1.  **CPU (JavaScript) Preparation:**
    * The `THREE.BufferGeometry` is created with two position attributes: `position` (current shape) and `targetPosition` (next shape).
    * When the user triggers a shape change, the script calls `updateMorphTarget(newIndex)`, which copies the target coordinates into the `targetPosition` buffer.
2.  **GPU (GLSL) Interpolation:**
    * A uniform variable, `uMix` (ranging from `0.0` to `1.0`), is passed to the shader.
    * The final visible position of every particle is calculated using the GLSL `mix` function:
        ```glsl
        vec3 mixedPos = mix(position, targetPosition, uMix);
        ```
3.  **Real-time Control:**
    * The `uExpansion` uniform, calculated based on the thumb-index pinch distance (e.g., `(dist - 0.05) * 5.0`), is then applied to the normalized mixed position, creating the explosion effect:
        ```glsl
        vec3 finalPos = mixedPos + (normalize(mixedPos) * uExpansion * 8.0);
        ```
This process ensures all particle logic is handled in parallel by the GPU, maintaining high frame rates despite the complex calculations and large number of particles.

## 🖼️ Debug UI

For easy debugging and verification of the computer vision pipeline, the application includes a detailed overlay:

* **Hand Skeleton:** A red and green overlay confirms MediaPipe is accurately tracking the 21 landmarks on the camera feed. 
* **Data Panel:** Displays raw sensor values:
    * `PINCH DISTANCE`: The normalized distance between thumb and index (used to drive expansion).
    * `HAND X`: The normalized horizontal position (used to drive rotation).

## 💡 Potential Future Enhancements

* **Gesture-Triggered Switching:** Replace the UI buttons with a specific gesture (e.g., holding up two fingers, a thumbs-up, or swiping) to initiate the morph.
* **Hand-Specific Color:** Use different hands to control separate parameters (e.g., Left Hand = Color, Right Hand = Expansion).
* **Custom Particle Texture:** Load a texture (e.g., a star or spark) instead of using the default circular point for richer visual effects.

---