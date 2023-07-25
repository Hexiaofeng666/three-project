
<template>
    <div class="btn">
        <button @click="back" v-if="showBack">返回</button>
        <div v-else>
            <div v-for="(menu,index) in menus" :key="index">
                <button @click="show(menu)">{{ menu.name }}</button>
            </div>
        </div>
    </div>
    <div class="container">
        <dragon v-if="menus[0].show"/>
        <sceneRoam v-if="menus[1].show"/>
        <rotating-cube v-if="menus[2].show"/>
        <ball v-if="menus[3].show"/>
    </div>
</template>
<script setup lang="ts">

import dragon from '../components/dragon.vue'
import sceneRoam from '../components/sceneRoam.vue'
import rotatingCube from '../components/rotatingCube.vue'
import ball from '../components/ball.vue'
import {ref} from 'vue'
// import TheWelcome from './components/TheWelcome.vue'
const showBack = ref(false)
const menus = ref([{
    name: '动态小龙',
    show: false
},
{
    name: '场景漫游',
    show: false
},
{
    name: '旋转的正方体',
    show: false
},
{
    name: '球',
    show: false
},
])
const back = ()=>{
    showBack.value=false
    menus.value.forEach(el => {
        el.show = false
    });
}
const show = (menu: any)=>{
    menu.show = true
    showBack.value=true
}
</script>

<style scoped>
.btn{
    position: fixed;
    left: 20px;
    top: 20px;
    z-index: 99;
}
.container{
    /* position: fixed; */
    width: 100%;
    height: 100%;
}
</style>
