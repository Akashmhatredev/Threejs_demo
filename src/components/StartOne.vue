<template>
  <div ref="sceneContainer" class="scene"></div>
</template>

<script>
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
export default {
  name: "StartOne",
  mounted() {
    this.initThree();
    this.animate();
    // window.addEventListener("mousemove", this.onMouseMove);
  },
  beforeUnmount() {
    // window.removeEventListener("mousemove", this.onMouseMove);
  },
  methods: {
    initThree() {
      const w = window.innerWidth;
      const h = window.innerHeight;

      // Renderer
      this.$options.renderer = new THREE.WebGLRenderer({ antialias: true });
      this.$options.renderer.setSize(w, h);
      this.$refs.sceneContainer.appendChild(this.$options.renderer.domElement);

      // Camera
      const fov = 75;
      const aspect = w / h;
      const near = 0.1;
      const far = 10;
      this.$options.camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
      this.$options.camera.position.z = 2;

      // Scene
      this.$options.scene = new THREE.Scene();
      
      const controls = new OrbitControls(this.$options.camera, this.$options.renderer.domElement);
      controls.enableDamping = true;
      controls.dampingFactor = 0.05;
      controls.rotateSpeed = 0.5;
      this.$options.controls = controls;

      // Mesh (Icosahedron)
      const geometry = new THREE.IcosahedronGeometry(1.0, 2);
      const material = new THREE.MeshStandardMaterial({ color: 0xffffff , flatShading: true});
      this.$options.mesh = new THREE.Mesh(geometry, material);
      this.$options.scene.add(this.$options.mesh);

      const hemiLight = new THREE.HemisphereLight(0x0099ff, 0xaa5500)
      this.$options.scene.add(hemiLight);
      
      const wireMat = new THREE.MeshBasicMaterial({ color: 0xccff , wireframe: true});
      this.$options.wireMesh = new THREE.Mesh(geometry, wireMat);
      this.$options.wireMesh.scale.setScalar(1.001)
      this.$options.mesh.add(this.$options.wireMesh);
      // Store mouse position
      this.$options.mouseX = 0;
      this.$options.mouseY = 0;
    },
    // animate() {
    //   requestAnimationFrame(this.animate);

    //   // Smoothly rotate the camera based on mouse movement
    //   this.$options.camera.rotation.y += (this.$options.mouseX - this.$options.camera.rotation.y) * 0.05;
    //   this.$options.camera.rotation.x += (this.$options.mouseY - this.$options.camera.rotation.x) * 0.05;

    //   this.$options.renderer.render(this.$options.scene, this.$options.camera);
    // },
    animate(t=0) {
      requestAnimationFrame(this.animate);
      this.$options.mesh.rotation.y = t * 0.0001
        // Update controls
      this.$options.controls.update();

      this.$options.renderer.render(this.$options.scene, this.$options.camera);
    },
    onMouseMove(event) {
      // Normalize mouse movement (-0.5 to 0.5) and map to rotation angles
      this.$options.mouseX = (event.clientX / window.innerWidth - 0.5) * Math.PI;
      this.$options.mouseY = (event.clientY / window.innerHeight - 0.5) * Math.PI;
    },
  },
};
</script>

<style scoped>
.scene {
  width: 100vw;
  height: 100vh;
}
</style>
