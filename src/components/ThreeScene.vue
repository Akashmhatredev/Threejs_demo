<template>
    <div ref="sceneContainer" class="scene"></div>
  </template>
  
  <script>
  import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
  
  export default {
    name: "ThreeScene",
    mounted() {
      this.initThree();
      this.animate();
    },
    methods: {
      initThree() {
        this.$options.scene = new THREE.Scene();
        this.$options.camera = new THREE.PerspectiveCamera(
          75,
          window.innerWidth / window.innerHeight,
          0.1,
          1000
        );
        this.$options.camera.position.z = 3;

        this.$options.renderer = new THREE.WebGLRenderer({ antialias: true });
        this.$options.renderer.setSize(window.innerWidth, window.innerHeight);
        this.$refs.sceneContainer.appendChild(this.$options.renderer.domElement);
        const controls = new OrbitControls(this.$options.camera, this.$options.renderer.domElement);
        controls.enableDamping = true;
        controls.dampingFactor = 0.05;
        controls.rotateSpeed = 0.5;
        this.$options.controls = controls;
        // Create a cube
        const geometry = new THREE.BoxGeometry();
        const material = new THREE.MeshStandardMaterial({ color: 0xffff00 });
        this.$options.cube = new THREE.Mesh(geometry, material);
        this.$options.scene.add(this.$options.cube);
  
        const hemiLight = new THREE.HemisphereLight(0xffffff, 0xff000)
        this.$options.scene.add(hemiLight);
      
        // Store mouse position
        this.$options.mouseX = 0;
        this.$options.mouseY = 0;
      },
      animate(t=0) {
        requestAnimationFrame(this.animate);
        this.$options.cube.rotation.y = t * 0.0001
        this.$options.controls.update();
        this.$options.renderer.render(this.$options.scene, this.$options.camera);
      }
    },
  };
  </script>
  
  <style scoped>
  .scene {
    width: 100vw;
    height: 100vh;
  }
  </style>
  