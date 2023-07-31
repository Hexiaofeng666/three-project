<template>
  <button style="position: absolute;right: 20px; top: 20px;" @click="reload">重置</button>
  <div id="my-three"></div>
</template>

<script lang="ts" setup>
// 模板
import * as THREE from 'three'
import * as CANNON from 'cannon'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'
import { onMounted, onBeforeUnmount, ref } from 'vue'
//创建一个三维场景
const scene = new THREE.Scene()

const world = new CANNON.World();
world.gravity.set(0, -9.82, 0);
world.broadphase = new CANNON.NaiveBroadphase();

const geometry = new THREE.BoxGeometry(1, 1, 1);
const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
const mesh = new THREE.Mesh(geometry, material);
mesh.position.set(0,5000,0)
scene.add(mesh);


const shape = new CANNON.Box(new CANNON.Vec3(0.5, 0.5, 0.5));
// mass质量
const body = new CANNON.Body({ mass: 10 ,position: new CANNON.Vec3(0, 15, 0)});
body.addShape(shape);
world.addBody(body);

// 设置边界
const groundShape = new CANNON.Plane();
const groundBody = new CANNON.Body({ mass: 0 });
groundBody.addShape(groundShape);
groundBody.quaternion.setFromAxisAngle(new CANNON.Vec3(1, 0, 0), -Math.PI / 2); // 设置地面的朝向
groundBody.position.set(0, -1, 0); // 将地面放置在原点的下方一段距离
world.addBody(groundBody);



//添加光源 //会照亮场景里的全部物体（氛围灯），前提是物体是可以接受灯光的，这种灯是无方向的，即不会有阴影。
const ambient = new THREE.AmbientLight(0xffffff, 0.4)
scene.add(ambient)

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

const reload = ()=> {
  body.position.set(0,15,0)
}
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

  world.step(1 / 60);

  // 同步物体的位置和旋转信息
  mesh.position.copy(body.position);
  mesh.quaternion.copy(body.quaternion);


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
