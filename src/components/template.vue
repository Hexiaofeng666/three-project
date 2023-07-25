<template>
    <div id="my-three"></div>
  </template>
  
  <script lang="ts" setup>
  // 模板
  import * as THREE from 'three'
  import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'
  import { onMounted, onBeforeUnmount, ref } from 'vue'
  //创建一个三维场景
  const scene = new THREE.Scene()

  
  //添加光源 //会照亮场景里的全部物体（氛围灯），前提是物体是可以接受灯光的，这种灯是无方向的，即不会有阴影。
  const ambient = new THREE.AmbientLight(0xffffff, 0.4)
  const light = new THREE.PointLight(0xffffff, 1) //点光源，color:灯光颜色，intensity:光照强度
  
  //创建一个透视相机，窗口宽度，窗口高度
  const width = window.innerWidth,
    height = window.innerHeight
  const camera = new THREE.PerspectiveCamera(45, width / height, 1, 1000)
  //设置相机位置
  camera.position.set(30, 30, 30)
  //设置相机方向
  camera.lookAt(0, 0, 0)
  
  //创建辅助坐标轴
  const axesHelper = new THREE.AxesHelper(200) //参数200标示坐标系大小，可以根据场景大小去设置
  scene.add(axesHelper)
  
  //创建一个WebGL渲染器
  const renderer = new THREE.WebGLRenderer()
  renderer.setSize(width, height) //设置渲染区尺寸
  renderer.setClearColor(0xffffff, 1) // 设置背景颜色为白色
  renderer.render(scene, camera) //执行渲染操作、指定场景、相机作为参数
  
  const controls = new OrbitControls(camera, renderer.domElement) //创建控件对象
  controls.addEventListener('change', () => {
    // console.log('change');
  
    renderer.render(scene, camera) //监听鼠标，键盘事件
  })
  
  let timer = ref()
  onMounted(() => {
    console.log(renderer);
    document.getElementById('my-three')?.appendChild(renderer.domElement)
  })
  onBeforeUnmount(() => {
    clearInterval(timer.value)
  })
  
  // 渲染函数
  function animate() {
    requestAnimationFrame(animate)

  
    // 渲染场景
    renderer.render(scene, camera)
  }
  
  animate()
  </script>
  
  <style lang="scss">
  body {
    margin: 0;
    padding: 0;
  }
  #my-three {
    // width: 300px;
    // height: 300px;
    // overflow: hidden;
  }
  </style>
  