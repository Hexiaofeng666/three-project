<template>
    <div>
      <div id="container" ref="canvas" />
    </div>
  </template>
  
  <script>
  import { ref, onMounted, onBeforeUnmount } from 'vue';
  import * as THREE from 'three';
  import { FirstPersonControls } from 'three/examples/jsm/controls/FirstPersonControls.js';
  import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js'
  
  export default {
    setup() {
      const canvas = ref(null);
      let mesh, camera, scene, renderer, firstPersonControls, clock;
  
      const init = () => {
        createScene();
        loadObj();
        createLight();
        createCamera();
        createRender();
        createFlyControls();
        render();
      };
  
      // 创建场景
      const createScene = () => {
        scene = new THREE.Scene();
      };
  
      // 加载OBJ模型
      const loadObj = () => {
        const loader = new GLTFLoader()
  
        loader.load(`sea_keep_lonely_watcher.glb`, (loadedMesh) => {
          mesh = loadedMesh.scene;
          loadedMesh.scene.position.set(0.2, 0.2, 0.2);
          setRandomColors(loadedMesh);
          scene.add(mesh);
        });
      };
  
      const setRandomColors = (object) => {
        const children = object.children;
  
        if (children && children.length > 0) {
          children.forEach(function (e) {
            setRandomColors(e);
          });
        } else {
          if (object instanceof THREE.Mesh) {
            const colorIndex = Math.floor(Math.random() * 3); // 0~2的随机数
            const colorArray = [
              new THREE.Color(0xff0000),
              new THREE.Color(0x00ff00),
              new THREE.Color(0x0000ff),
            ];
            object.material.emissive = colorArray[colorIndex];
            object.material.color = colorArray[colorIndex];
            object.material.transparent = true;
            object.material.opacity = 0.3;
          }
        }
      };
  
      // 创建光源
      const createLight = () => {
        // 环境光
        const ambientLight = new THREE.AmbientLight(0x383838);
        scene.add(ambientLight);
  
        const spotLight = new THREE.SpotLight(0xffffff);
        spotLight.position.set(200, 200, 200);
        spotLight.castShadow = true;
        scene.add(spotLight);
      };
  
      // 创建相机
      const createCamera = () => {
        const width = window.innerWidth
        const height = window.innerHeight
        const k = width / height;
  
        camera = new THREE.PerspectiveCamera(35, k, 0.1, 1000);
        camera.position.set(-45, 60, 457)
  camera.lookAt(0, 60, 0)
        scene.add(camera);
      };
  
      // 创建渲染器
      const createRender = () => {
        renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
        renderer.setSize(canvas.value.clientWidth, canvas.value.clientHeight);
        renderer.shadowMap.enabled = true;
        renderer.shadowMap.type = THREE.PCFSoftShadowMap;
        renderer.setClearColor(0x3f3f3f, 1);
        canvas.value.appendChild(renderer.domElement);
      };
  
      // 渲染
      const render = () => {
        const delta = clock.getDelta();
        firstPersonControls.update(delta);
        renderer.render(scene, camera);
        requestAnimationFrame(render);
      };
  
      // 创建第一人称控件
      const createFlyControls = () => {
        clock = new THREE.Clock();
        firstPersonControls = new FirstPersonControls(camera, renderer.domElement);
        firstPersonControls.lookSpeed = 0.1;
        firstPersonControls.movementSpeed = 20;
        firstPersonControls.noFly = true;
        firstPersonControls.lookVertical = true;
        firstPersonControls.constrainVertical = true;
        firstPersonControls.verticalMin = 1.0;
        firstPersonControls.verticalMax = 2.0;
        firstPersonControls.lon = -150;
        firstPersonControls.lat = 120;
      };
  
      onMounted(() => {
        init();
      });
  
      onBeforeUnmount(() => {
        renderer.dispose();
      });
  
      return {
        canvas,
      };
    },
  };
  </script>
  
  <style>
  #container {
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
  }
  </style>
  