<template>
  <div ref="canvasWrapper"></div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
import * as THREE from 'three';
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';

const canvasWrapper = ref(null);
let scene, camera, renderer, controls, cube;

// 监听键盘事件
const keyboardState = {};

const onKeyDown = (event) => {
  keyboardState[event.code] = true;
};

const onKeyUp = (event) => {
  keyboardState[event.code] = false;
};

// 初始化场景、相机、渲染器和控制器
onMounted(() => {
  scene = new THREE.Scene();
  camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
  renderer = new THREE.WebGLRenderer();
  renderer.setSize(window.innerWidth, window.innerHeight);
  canvasWrapper.value.appendChild(renderer.domElement);

  // 添加灯光
  const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
  scene.add(ambientLight);
  const directionalLight = new THREE.DirectionalLight(0xffffff, 0.5);
  scene.add(directionalLight);

  // 添加立方体模型
  const geometry = new THREE.BoxGeometry();
  const material = new THREE.MeshPhongMaterial({ color: 0x00ff00 });
  cube = new THREE.Mesh(geometry, material);
  scene.add(cube);

  // 设置相机初始位置和朝向
  camera.position.set(5, 5, 5);
  camera.lookAt(scene.position);

  // 添加轨道控制器
  controls = new OrbitControls(camera, renderer.domElement);

  // 监听键盘事件
  document.addEventListener('keydown', onKeyDown);
  document.addEventListener('keyup', onKeyUp);

  // 渲染场景
  const animate = () => {
    requestAnimationFrame(animate);

    // 根据键盘事件更新相机位置
    const step = 0.1; // 相机移动的步长

    // 根据相机的朝向来计算移动方向
    const cameraDirection = new THREE.Vector3();
    camera.getWorldDirection(cameraDirection);

    // 向前后移动
    if (keyboardState['ArrowUp']) {
      camera.position.add(cameraDirection.multiplyScalar(-step)); // 向前移动
    }
    if (keyboardState['ArrowDown']) {
      camera.position.add(cameraDirection.multiplyScalar(step)); // 向后移动
    }

    // 向左右移动，忽略y轴方向
    const cameraRight = new THREE.Vector3();
    cameraRight.crossVectors(cameraDirection, camera.up).normalize();
    const cameraUp = new THREE.Vector3(0, 1, 0);
    cameraUp.applyQuaternion(camera.quaternion);
    const cameraForward = new THREE.Vector3().crossVectors(cameraRight, cameraUp).normalize();

    if (keyboardState['ArrowLeft']) {
      camera.position.add(cameraRight.multiplyScalar(-step)); // 向左移动
    }
    if (keyboardState['ArrowRight']) {
      camera.position.add(cameraRight.multiplyScalar(step)); // 向右移动
    }

    controls.update();
    renderer.render(scene, camera);
  };

  animate();
});

// 在组件销毁时移除事件监听
onUnmounted(() => {
  document.removeEventListener('keydown', onKeyDown);
  document.removeEventListener('keyup', onKeyUp);
});
</script>

<style>
body { margin: 0; overflow: hidden; }
canvas { display: block; }
</style>
