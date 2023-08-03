<template>
  <div id="my-three"></div>
</template>
  
<script setup>
// 模板
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'
import { onMounted, onBeforeUnmount, ref } from 'vue'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js'
//创建一个三维场景
const scene = new THREE.Scene()

const loader = new GLTFLoader()

//添加光源 //会照亮场景里的全部物体（氛围灯），前提是物体是可以接受灯光的，这种灯是无方向的，即不会有阴影。
const ambient = new THREE.AmbientLight(0xffffff, 0.4)
scene.add(ambient)

//创建一个透视相机，窗口宽度，窗口高度
const width = window.innerWidth,
  height = window.innerHeight
const camera = new THREE.PerspectiveCamera(45, width / height, 1, 10000)
//设置相机位置
camera.position.set(30, 30, 30)
//设置相机方向
camera.lookAt(0, 0, 0)

// create an AudioListener and add it to the camera
const listener = new THREE.AudioListener();
camera.add(listener);

// create the PositionalAudio object (passing in the listener)
const sound = new THREE.PositionalAudio(listener);

// load a sound and set it as the PositionalAudio object's buffer
const audioLoader = new THREE.AudioLoader();
audioLoader.load('music.ogg', function (buffer) {
  sound.setBuffer(buffer);
  sound.setRefDistance(20);
  sound.play();
});

const group = new THREE.Group();

let people = ref()
let mixer = null
loader.load('woman.glb', function (gltf) {
  people.value = gltf
  gltf.scene.position.set(0, -2, 0)
  people.value = gltf
  // 返回的场景对象gltf.scene插入到threejs场景中
  group.add(gltf.scene)
  //包含关键帧动画的模型作为参数创建一个播放器
  mixer = new THREE.AnimationMixer(gltf.scene)
  //  获取gltf.animations[0]的第一个clip动画对象
  const clipAction = mixer.clipAction(gltf.animations[0]) //创建动画clipAction对象
  clipAction.play() //播放动画

  // 如果想播放动画,需要周期性执行`mixer.update()`更新AnimationMixer时间数据
  const clock = new THREE.Clock()
  function loop() {
    requestAnimationFrame(loop)
    //clock.getDelta()方法获得loop()两次执行时间间隔
    const frameT = clock.getDelta()
    // 更新播放器相关的时间
    mixer.update(frameT)
    renderer.render(scene, camera)
  }
  loop()
  // render()
})

let microphone = ref()
loader.load('microphone.glb', function (gltf) {
  gltf.scene.scale.set(0.08, 0.08, 0.08);
  gltf.scene.position.set(0, -2, 1.5)
  microphone.value = gltf
  group.add(gltf.scene)
})
group.add(sound)
scene.add( group );



//创建辅助坐标轴
// const axesHelper = new THREE.AxesHelper(200) //参数200标示坐标系大小，可以根据场景大小去设置
// scene.add(axesHelper)

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
  document.getElementById('my-three')?.appendChild(renderer.domElement)
})
onBeforeUnmount(() => {
  clearInterval(timer.value)
})

let direction = true
// 渲染函数
function animate() {
  requestAnimationFrame(animate)
  if (direction) {
    group.position.x += 0.1
  }
  else group.position.x-= 0.1
  if (group.position.x>10||group.position.x<-10) {
    direction = !direction
  }

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
  