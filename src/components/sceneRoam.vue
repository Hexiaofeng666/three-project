<template>
    <div id="my-three" ref="box"></div>
</template>

<script lang="ts" setup>
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'

import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js'
import { onMounted, ref } from 'vue'

const scene = new THREE.Scene()

const loader = new GLTFLoader()
// loader.load('sea_keep_lonely_watcher.glb',(gltf: any)=>{
//     gltf.scene.position.set(0,0,0)
//     scene.add(gltf.scene)
//     renderer.render(scene,camera)
// })



// 添加环境光
const ambient = new THREE.AmbientLight(0xffffff, 0.8)
scene.add(ambient)

const width = window.innerWidth
const height = window.innerHeight
//创建一个透视相机，窗口宽度，窗口高度
const camera = new THREE.PerspectiveCamera(45, width / height, 1, 10000)
camera.position.set(-45, 60, 457)
camera.lookAt(0, 60, 0)
console.log(camera);
const cameraDirection = new THREE.Vector3();
console.log(camera.getWorldDirection(cameraDirection));





//创建辅助坐标轴
const axesHelper = new THREE.AxesHelper(200) //参数200标示坐标系大小，可以根据场景大小去设置
scene.add(axesHelper)


const renderer = new THREE.WebGLRenderer()
renderer.setSize(width, height)
renderer.setClearColor(0xffffff, 1) // 设置背景颜色为白色
renderer.render(scene, camera)

// 添加轨道控制器
const controls = new OrbitControls(camera, renderer.domElement) //创建控件对象
controls.addEventListener('change', () => {
    console.log(camera);
    
    renderer.render(scene, camera) //监听鼠标，键盘事件
})

const moveSpeed = 0.1 // 移动速度
function onKeyDown(event: any) {
    console.log(event);
    switch (event.code) {
        case 'ArrowUp':
            camera.position.z += 1
            break;
        case 'ArrowDown':
            camera.position.z -= 1
            break;
        case 'ArrowLeft':
            camera.position.x += 1
            break;
        case 'ArrowRight':
            camera.position.x -= 1
            break;
        case 'KeyJ':
            camera.position.y += 1
            break;
        case 'KeyK':
            camera.position.y -= 1
            break;
        default:
            break;
    }
    renderer.render(scene, camera)
}

// 监听键盘事件
window.addEventListener('keydown', onKeyDown)

const box = ref()
onMounted(() => {
    box.value?.appendChild(renderer.domElement)
    //   document.getElementById('my-three')?.appendChild(renderer.domElement)
})
</script>