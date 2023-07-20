<template>
  <div id="my-three"></div>
</template>

<script lang="ts" setup>
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js'
import { onMounted, onBeforeUnmount, ref } from 'vue'
//创建一个三维场景
const scene = new THREE.Scene()

const loader = new GLTFLoader()

// let tenement = ref()
// loader.load('sea_keep_lonely_watcher.glb', function (gltf: any) {
//   console.log('控制台查看加载gltf1文件返回的对象结构', gltf)
//   console.log('gltf1对象场景属性', gltf.scene)
//   gltf.scene.position.set(0, 0, 0)
//   tenement.value = gltf
//   // 返回的场景对象gltf.scene插入到threejs场景中
//   scene.add(gltf.scene)
//   // 计算包围盒
//   const bbox = new THREE.Box3().setFromObject(gltf.scene);
//   const size = new THREE.Vector3();
//   bbox.getSize(size);
//   console.log("模型的大小：", size.x, size.y, size.z);

//   renderer.render(scene, camera)
// })

let people = ref()
let actions = ref()
let mixer: any = null
loader.load('black_dragon_with_idle_animation.glb', function (gltf: any) {
  people.value = gltf
  console.log('控制台查看加载gltf2文件返回的对象结构', gltf)
  console.log('gltf2对象场景属性', gltf.scene)
  gltf.scene.position.set(0, 0, 0)
  people.value = gltf
  // 返回的场景对象gltf.scene插入到threejs场景中
  scene.add(gltf.scene)
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
  }
  loop()
  renderer.render(scene, camera)
  // render()
})

//添加光源 //会照亮场景里的全部物体（氛围灯），前提是物体是可以接受灯光的，这种灯是无方向的，即不会有阴影。
const ambient = new THREE.AmbientLight(0xffffff, 0.4)
const light = new THREE.PointLight(0xffffff, 1) //点光源，color:灯光颜色，intensity:光照强度
const spotLight = new THREE.SpotLight(0xffffff, 1.0)
scene.add(spotLight) //光源添加到场景中
const directionalLight = new THREE.DirectionalLight(0xffffff, 1)
directionalLight.position.set(100, 60, 50)
scene.add(directionalLight)
scene.add(ambient)
light.position.set(200, 300, 400)
scene.add(light)

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

// 键盘事件处理
const moveSpeed = 0.1 // 移动速度
let moveDirection = new THREE.Vector3() // 移动方向向量
const keyState: any = {} // 记录按键状态

function onKeyDown(event: any) {
  keyState[event.code] = true
}

function onKeyUp(event: any) {
  keyState[event.code] = false
}

window.addEventListener('keydown', onKeyDown, false)
window.addEventListener('keyup', onKeyUp, false)

// 渲染函数
function animate() {
  requestAnimationFrame(animate)

  // 根据按键设置移动方向
  moveDirection.set(0, 0, 0)
  if (keyState['ArrowUp']) moveDirection.z += 1
  if (keyState['ArrowDown']) moveDirection.z -= 1
  if (keyState['ArrowLeft']) moveDirection.x += 1
  if (keyState['ArrowRight']) moveDirection.x -= 1

  // 归一化移动方向向量并乘以移动速度
  moveDirection.normalize().multiplyScalar(moveSpeed)

  // 移动模型
  // console.log(people.value);
  if (people.value) {
    people.value.scene.position.add(moveDirection)
    camera.position.add(moveDirection)
  }
  

  // 渲染场景
  renderer.render(scene, camera)
}

animate()

// 创建一个时钟对象Clock
const clock = new THREE.Clock()
function render() {
  requestAnimationFrame(render)
  //  console.log(mixer);
  if (mixer !== null) {
    //clock.getDelta()方法获得两帧的时间间隔
    // 更新播放器相关的时间
    mixer.update(clock.getDelta())
    renderer.render(scene, camera)
  }
}
// render()
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
