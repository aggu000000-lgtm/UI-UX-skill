# 3D Web Integration Reference

> **2026 STANDARDS**: WebGPU is now production-ready across all browsers. Three.js has reached r171+ with seamless WebGPU integration. Spline AI enables no-code 3D design. This guide covers integrating 3D into modern web interfaces.

---

## WEBGPU: THE NEW STANDARD

### Browser Support (2026)

| Browser | WebGPU Support | Minimum Version |
|---------|---------------|-----------------|
| Chrome/Edge | Full | v113+ (May 2023) |
| Firefox | Full | v141+ (Windows), v145+ (macOS ARM) |
| Safari | Full | v26+ (September 2025) |
| iOS Safari | Full | v26+ |

**Global Coverage**: ~95% of users have WebGPU-capable browsers.

### Three.js WebGPU Setup

```javascript
// Option 1: Zero-config WebGPU (r171+)
import * as THREE from 'three';
import { WebGPURenderer } from 'three/addons/renderers/WebGPURenderer.js';

// Automatic WebGL fallback
const renderer = new WebGPURenderer({
  antialias: true,
  alpha: true
});
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

// Fallback for older browsers
if (!renderer.capabilities.isWebGPU) {
  console.warn('WebGPU not available, using WebGL');
  // Fallback to WebGLRenderer
}

document.body.appendChild(renderer.domElement);
```

### React Three Fiber + WebGPU

```jsx
import { Canvas } from '@react-three/fiber';
import { WebGPURenderer } from '@react-three/drei';

function Scene() {
  return (
    <Canvas
      gl={async () => {
        const renderer = new WebGPURenderer();
        await renderer.init();
        return renderer;
      }}
      camera={{ position: [0, 0, 5] }}
    >
      <ambientLight intensity={0.5} />
      <mesh>
        <boxGeometry args={[1, 1, 1]} />
        <meshStandardMaterial color="orange" />
      </mesh>
    </Canvas>
  );
}
```

---

## THREE.JS CORE CONCEPTS

### Basic Scene Setup

```javascript
import * as THREE from 'three';
import { OrbitControls } from 'three/addons/controls/OrbitControls.js';
import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';

// Scene
const scene = new THREE.Scene();
scene.background = new THREE.Color('#0a0a0a');

// Camera
const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  1000
);
camera.position.set(0, 2, 5);

// Renderer
const renderer = new THREE.WebGLRenderer({ 
  antialias: true,
  alpha: true 
});
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
renderer.toneMapping = THREE.ACESFilmicToneMapping;
renderer.toneMappingExposure = 1.2;
document.body.appendChild(renderer.domElement);

// Controls
const controls = new OrbitControls(camera, renderer.domElement);
controls.enableDamping = true;
controls.dampingFactor = 0.05;

// Lights
const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
scene.add(ambientLight);

const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
directionalLight.position.set(5, 5, 5);
scene.add(directionalLight);

// Animation Loop
function animate() {
  requestAnimationFrame(animate);
  controls.update();
  renderer.render(scene, camera);
}
animate();
```

### Loading 3D Models (GLTF/GLB)

```javascript
const loader = new GLTFLoader();

loader.load(
  '/models/product.glb',
  (gltf) => {
    const model = gltf.scene;
    
    // Center and scale model
    const box = new THREE.Box3().setFromObject(model);
    const center = box.getCenter(new THREE.Vector3());
    model.position.sub(center);
    
    // Auto-scale to fit
    const size = box.getSize(new THREE.Vector3());
    const maxDim = Math.max(size.x, size.y, size.z);
    const scale = 2 / maxDim;
    model.scale.setScalar(scale);
    
    scene.add(model);
  },
  (progress) => {
    console.log('Loading:', (progress.loaded / progress.total * 100) + '%');
  },
  (error) => {
    console.error('Error loading model:', error);
  }
);
```

---

## THREE.JS SHADER LANGUAGE (TSL)

TSL lets you write shaders in JavaScript that compile to both WGSL (WebGPU) and GLSL (WebGL).

```javascript
import * as THREE from 'three';
import { nodeObject } from 'three/tsl';

// Create procedural texture
const proceduralTexture = nodeObject(
  new THREE.NoiseNode(0.1)
).convertToTexture();

const material = new THREE.MeshStandardNodeMaterial({
  map: proceduralTexture,
  metalness: 0.8,
  roughness: 0.2
});
```

---

## SPLINE INTEGRATION

### React Spline Component

```jsx
import { Spline } from '@splinetool/react-spline';

function Hero3D() {
  return (
    <div style={{ height: '100vh', width: '100%' }}>
      <Spline
        scene="https://prod.spline.design/6Wq1Q7YGyM-iab9i/scene.splinecode"
        style={{
          width: '100%',
          height: '100%'
        }}
        onLoad={(spline) => {
          console.log('Spline loaded');
        }}
      />
    </div>
  );
}
```

### Spline Events

```jsx
function InteractiveSpline() {
  const handleMouseDown = (e) => {
    console.log('Clicked:', e.target.name);
  };

  return (
    <Spline
      scene="your-scene.splinecode"
      onMouseDown={handleMouseDown}
    />
  );
}
```

### Spline WebGPU Performance

```css
.spline-container {
  will-change: transform;
  transform: translateZ(0);
  contain: layout style paint;
}

/* Lazy load non-critical scenes */
.spline-lazy {
  opacity: 0;
  transition: opacity 400ms ease-out;
}

.spline-lazy.loaded {
  opacity: 1;
}
```

---

## PERFORMANCE BEST PRACTICES

### Geometry Optimization

```javascript
// Merge geometries for performance
const mergedGeometry = BufferGeometryUtils.mergeGeometries(geometries);

// Use instancing for repeated objects
const instancedMesh = new THREE.InstancedMesh(geometry, material, 100);
for (let i = 0; i < 100; i++) {
  const matrix = new THREE.Matrix4();
  matrix.setPosition(i * 2, 0, 0);
  instancedMesh.setMatrixAt(i, matrix);
}
```

### Texture Optimization

```javascript
// Compress textures
const textureLoader = new THREE.TextureLoader();
const texture = textureLoader.load('/texture.webp', (tex) => {
  tex.minFilter = THREE.LinearMipmapLinearFilter;
  tex.magFilter = THREE.LinearFilter;
  tex.generateMipmaps = true;
  tex.anisotropy = renderer.capabilities.getMaxAnisotropy();
});
```

### Frame Rate Control

```javascript
// Limit to 60fps
let lastTime = 0;
const targetFPS = 60;
const frameInterval = 1000 / targetFPS;

function animate(currentTime) {
  requestAnimationFrame(animate);
  
  const delta = currentTime - lastTime;
  
  if (delta > frameInterval) {
    lastTime = currentTime - (delta % frameInterval);
    renderer.render(scene, camera);
  }
}
```

### Lazy Loading Strategy

```javascript
// Load 3D content after main content
window.addEventListener('load', () => {
  setTimeout(() => {
    const loader = new GLTFLoader();
    loader.load('/3d/product.glb', (gltf) => {
      scene.add(gltf.scene);
    });
  }, 2000); // Delay 2s after page load
});
```

---

## RESPONSIVE 3D

```javascript
// Handle resize
window.addEventListener('resize', () => {
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth, window.innerHeight);
  
  // Adjust for mobile
  if (window.innerWidth < 768) {
    camera.position.set(0, 1, 5);
  } else {
    camera.position.set(0, 2, 5);
  }
});
```

---

## ACCESSIBILITY

```css
/* Reduced motion fallback */
@media (prefers-reduced-motion: reduce) {
  .webgl-canvas {
    display: none;
  }
  
  .webgl-fallback {
    display: block;
    background: var(--hero-gradient);
  }
}
```

```html
<!-- Fallback for no WebGL/WebGPU -->
<canvas id="webgl"></canvas>
<noscript>
  <img src="hero-fallback.jpg" alt="Product showcase">
</noscript>
```

---

## TOOLS & RESOURCES

- **Three.js**: https://threejs.org/
- **React Three Fiber**: https://docs.pmnd.rs/react-three-fiber
- **Spline**: https://spline.design/
- **Three.js Marketplace**: https://threejs.market/
- **Sketchfab (3D Models)**: https://sketchfab.com/
- **Poly Pizza (Free 3D)**: https://poly.pizza/

---

## USE CASES

| Application | Technology | Best For |
|-------------|------------|----------|
| Product Configurator | Three.js + WebGPU | E-commerce |
| Interactive Portfolio | Spline + React | Design/Agency |
| Data Visualization | Three.js + D3 | Analytics |
| Immersive Storytelling | Three.js + GSAP | Marketing |
| Real-time Collaboration | Three.js + WebRTC | SaaS |

---

**Last Updated**: 2026-04-12
