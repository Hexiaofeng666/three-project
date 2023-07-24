<template>
    <div id="my-three" ref="box"></div>
</template>

<script lang="ts" setup>
import * as THREE from 'three'
// import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'
// import { FirstPersonControls } from 'three/examples/jsm/controls/FirstPersonControls';
import { PointerLockControls } from 'three/examples/jsm/controls/PointerLockControls';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js'
import { onMounted, ref, onBeforeUnmount } from 'vue'

const scene = new THREE.Scene()

const loader = new GLTFLoader()
loader.load('sea_keep_lonely_watcher.glb', (gltf: any) => {
    gltf.scene.position.set(0, 0, 0)
    scene.add(gltf.scene)
    renderer.render(scene, camera)
})



// 添加环境光
const ambient = new THREE.AmbientLight(0xffffff, 0.8)
scene.add(ambient)

const width = window.innerWidth
const height = window.innerHeight
//创建一个透视相机，窗口宽度，窗口高度
const camera = new THREE.PerspectiveCamera(45, width / height, 1, 10000)
camera.position.set(-45, 600, 457)
camera.lookAt(0, 60, 0)
console.log(camera);



//创建辅助坐标轴
// const axesHelper = new THREE.AxesHelper(200) //参数200标示坐标系大小，可以根据场景大小去设置
// scene.add(axesHelper)


const renderer = new THREE.WebGLRenderer()
renderer.setSize(width, height)
renderer.setClearColor(0xffffff, 1) // 设置背景颜色为白色
renderer.render(scene, camera)

// 创建PointerLockControls
const controls = new PointerLockControls(camera, renderer.domElement);

renderer.domElement.addEventListener('click', function () {
    controls.lock();
});

controls.addEventListener('lock', function () {
    // camera.position.set(-45, 600, 457)
    // renderer.domElement.style.display = 'none';
});

// controls.addEventListener('unlock', function () {
//     renderer.domElement.style.display = '';
// });

scene.add(controls.getObject());

let moveForward = false;
let moveBackward = false;
let moveLeft = false;
let moveRight = false;
let canJump = false;
const velocity = new THREE.Vector3();
const direction = new THREE.Vector3();
const onKeyDown = function (event: { code: any; }) {

    switch (event.code) {

        case 'ArrowUp':
        case 'KeyW':
            moveForward = true;
            break;

        case 'ArrowLeft':
        case 'KeyA':
            moveLeft = true;
            break;

        case 'ArrowDown':
        case 'KeyS':
            moveBackward = true;
            break;

        case 'ArrowRight':
        case 'KeyD':
            moveRight = true;
            break;

        case 'Space':
            if (canJump === true) velocity.y += 350;
            canJump = false;
            break;

    }

};

const onKeyUp = function (event: { code: any; }) {

    switch (event.code) {

        case 'ArrowUp':
        case 'KeyW':
            moveForward = false;
            break;

        case 'ArrowLeft':
        case 'KeyA':
            moveLeft = false;
            break;

        case 'ArrowDown':
        case 'KeyS':
            moveBackward = false;
            break;

        case 'ArrowRight':
        case 'KeyD':
            moveRight = false;
            break;

    }

};

document.addEventListener('keydown', onKeyDown);
document.addEventListener('keyup', onKeyUp);

// 渲染
const clock = new THREE.Clock()
let raycaster = new THREE.Raycaster( new THREE.Vector3(), new THREE.Vector3( 0, - 1, 0 ), 0, 10 );
let objects: never[] = [];
let prevTime = performance.now();
function render() {

requestAnimationFrame( render );

const time = performance.now();

if ( controls.isLocked === true ) {

    raycaster.ray.origin.copy( controls.getObject().position );
    // raycaster.ray.origin.y -= 10;

    const intersections = raycaster.intersectObjects( objects, false );

    const onObject = intersections.length > 0;

    const delta = ( time - prevTime ) / 1000;

    velocity.x -= velocity.x * 10.0 * delta;
    velocity.z -= velocity.z * 10.0 * delta;

    velocity.y -= 9.8 * 100.0 * delta; // 100.0 = mass

    direction.z = Number( moveForward ) - Number( moveBackward );
    direction.x = Number( moveRight ) - Number( moveLeft );
    direction.normalize(); // this ensures consistent movements in all directions

    if ( moveForward || moveBackward ) velocity.z -= direction.z * 400.0 * delta;
    if ( moveLeft || moveRight ) velocity.x -= direction.x * 400.0 * delta;

    if ( onObject === true ) {

        velocity.y = Math.max( 0, velocity.y );
        canJump = true;

    }

    controls.moveRight( - velocity.x * delta );
    controls.moveForward( - velocity.z * delta );

    controls.getObject().position.y += ( velocity.y * delta ); // new behavior

    if ( controls.getObject().position.y < 60 ) {

        velocity.y = 0;
        controls.getObject().position.y = 60;

        canJump = true;

    }

}

prevTime = time;

renderer.render( scene, camera );

}


const box = ref()
onMounted(() => {
    box.value?.appendChild(renderer.domElement)
    render()
    //   document.getElementById('my-three')?.appendChild(renderer.domElement)
})
onBeforeUnmount(() => {
    renderer.dispose();
});
</script>