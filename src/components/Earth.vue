<template>
    <div ref="sceneContainer" class="scene"></div>
  </template>
  
  <script>
  import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls.js";
import getStarfield from "src/jsFile/getStarfield";
import { getFresnelMat } from "src/jsFile/getFresneMat";
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

        this.$options.earthGroup = new THREE.Group();
        this.$options.earthGroup.rotation.z = -23.4 * Math.PI / 180;
        this.$options.scene.add(this.$options.earthGroup);
        new OrbitControls(this.$options.camera, this.$options.renderer.domElement);
        // controls.enableDamping = true;
        // controls.dampingFactor = 0.05;
        // controls.rotateSpeed = 0.5;
        // this.$options.controls = controls;
        const detail = 12;
        const loader = new THREE.TextureLoader();
        const geometry = new THREE.IcosahedronGeometry(1, detail);
        const material = new THREE.MeshStandardMaterial({
            map: loader.load("/textures/00_earthmap.jpg")
         });
        this.$options.earthMesh = new THREE.Mesh(geometry, material);
        this.$options.scene.add(this.$options.earthMesh);
  
        
        const lightsMat = new THREE.MeshBasicMaterial({
          transparent: true,
          opacity: 0.8,
          map: loader.load("/textures/03_earthlights1k.jpg"),
          blending: THREE.AdditiveBlending,
        });
        this.$options.lightsMesh = new THREE.Mesh(geometry, lightsMat)
        this.$options.scene.add(this.$options.lightsMesh);

        const cloudsMat = new THREE.MeshStandardMaterial({
          map: loader.load("./textures/04_earthcloudmap.jpg"),
          transparent: true,
          opacity: 0.5,
          blending: THREE.AdditiveBlending,
          alphaMap: loader.load('./textures/05_earthcloudmaptrans.jpg'),
          alphaTest: 0.3,
        });
        this.$options.cloudsMesh = new THREE.Mesh(geometry, cloudsMat);
        this.$options.cloudsMesh.scale.setScalar(1.003);
        this.$options.scene.add(this.$options.cloudsMesh);
        
        const fresnelMat = getFresnelMat();
        this.$options.glowMesh = new THREE.Mesh(geometry, fresnelMat);
        this.$options.glowMesh.scale.setScalar(1.01);
        this.$options.scene.add(this.$options.glowMesh);


        const start = getStarfield({numStars:1000})
        this.$options.scene.add(start);

        const sunLight = new THREE.DirectionalLight(0xffffff, 2.0);
        sunLight.position.set(-2, 0.5, 1.5);
        this.$options.scene.add(sunLight);
              
      },
      animate() {
        requestAnimationFrame(this.animate);
        this.$options.earthMesh.rotation.y += 0.002;
        this.$options.lightsMesh.rotation.y += 0.002;
        this.$options.cloudsMesh.rotation.y += 0.0023;
        this.$options.glowMesh.rotation.y += 0.002;
        // this.$options.controls.update();
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
  