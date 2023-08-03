<template>
  <div id="my-three"></div>
</template>
  
<script setup>
// 模板
import * as THREE from 'three'
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'
import { onMounted, onBeforeUnmount, ref } from 'vue'
//创建一个三维场景
const scene = new THREE.Scene()

// 创建一个空房间，画出它的五个面，并给个小贴图
for (let i = 0; i < 5; i++) {
  let geometry
  let position = [0, 0, 0]
  let material = new THREE.MeshLambertMaterial({ color: 0xFFFFFF })
  if (i == 0) {
    geometry = new THREE.BoxGeometry(50, 10, 1)
    position = [0, 5, -10]

  }
  else if (i == 1) {
    geometry = new THREE.BoxGeometry(1, 10, 20)
    position = [25, 5, 0]
  }
  else if (i == 2) {
    geometry = new THREE.BoxGeometry(50, 10, 1)
    position = [0, 5, 10]
  }
  else if (i == 3) {
    geometry = new THREE.BoxGeometry(1, 10, 20)
    position = [-25, 5, 0]
  }
  else if (i == 4) {
    geometry = new THREE.BoxGeometry(50, 1, 20)
    position = [0, 0, 0]
    material = new THREE.MeshLambertMaterial({ color: 0x788183 })
  }
  const mesh = new THREE.Mesh(geometry, material)
  mesh.position.set(...position)
  console.log(mesh);


  scene.add(mesh)
}

// 创建贴图
const textureLoader = new THREE.TextureLoader();
const images = ['imgs/back.jpeg', 'imgs/wangye.png', 'imgs/head-pic.webp', 'imgs/wangye3.webp','imgs/wangye4.webp','imgs/wangye5.webp',]

for (let i = 0; i < images.length; i++) {
  textureLoader.load(images[i], (texture) => {
    const planeGeometry = new THREE.PlaneGeometry(10, 5, 1, 1)
    const planeMaterial = new THREE.MeshLambertMaterial({ map: texture, side: THREE.DoubleSide });
    const plane = new THREE.Mesh(planeGeometry, planeMaterial);
    let position
    if (i == 0) {
      position = [-10, 5, -9.49]
    }
    else if (i == 1) {
      plane.rotation.y += Math.PI / 2
      position = [24.49, 5, 0]
    }
    else if (i == 2) {
      position = [-10, 5, 9.49]
    }
    else if (i == 3) {
      plane.rotation.y += Math.PI / 2
      position = [-24.49, 5, 0]
    }
    else if (i == 4) {
      position = [10, 5, -9.49]
    }
    else if (i == 5) {
      position = [10, 5, 9.49]
    }

    if (position) {
      plane.position.set(...position)
      scene.add(plane)
    }
  })
}

// 设置光线的目标点坐标
const targetPosition = new THREE.Vector3(0, 0, 0);
const target = new THREE.Object3D();
target.position.copy(targetPosition);





//添加光源 //会照亮场景里的全部物体（氛围灯），前提是物体是可以接受灯光的，这种灯是无方向的，即不会有阴影。
// const ambient = new THREE.AmbientLight(0xffffff, 0.4)
// scene.add(ambient)
const point = new THREE.PointLight(0xffffff);
point.position.set(25, 30, 10);
scene.add(point);

// 平行光
const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
// 两点确定一条直线，我们要找两个点，一个是我们设置的点(position)，一个是物体(target)
directionalLight.position.set(0, 30, 0);
directionalLight.target = target;
scene.add(directionalLight);

// 聚光灯
const spotLight = new THREE.SpotLight(0xffffff);
// 两点确定一条直线，我们要找两个点，一个是我们设置的点(position)，一个是物体(target)
spotLight.position.set(0, 200, 0);
spotLight.target = target;
// 设置发散的角度
spotLight.angle = Math.PI / 100
scene.add(spotLight);

//创建一个透视相机，窗口宽度，窗口高度
const width = window.innerWidth,
  height = window.innerHeight
const camera = new THREE.PerspectiveCamera(45, width / height, 1, 1000)
//设置相机位置
camera.position.set(30, 30, 30)
//设置相机方向
camera.lookAt(0, 30, 0)

//创建辅助坐标轴
const axesHelper = new THREE.AxesHelper(200) //参数200标示坐标系大小，可以根据场景大小去设置
scene.add(axesHelper)

//创建一个WebGL渲染器
const renderer = new THREE.WebGLRenderer()
renderer.setSize(width, height) //设置渲染区尺寸
// renderer.setClearColor(0xffffff, 1) // 设置背景颜色为白色
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

// 方向
let direction = true
// 渲染函数
function animate() {
  requestAnimationFrame(animate)
  console.log(spotLight.position);
  if (spotLight.position.x>250) {
    direction = false
  }
  else if (spotLight.position.x<-250) {
    direction = true
  }
  direction?spotLight.position.x+=1:spotLight.position.x-=1
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
  