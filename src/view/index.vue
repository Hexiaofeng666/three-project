
<template>
    <div class="btn">
        <button @click="back" v-if="showBack">返回</button>
        <div v-else>
            <div v-for="(menu,index) in menus" :key="index">
                <button @click="show(menu)">{{ menu.name }}</button>
            </div>
        </div>
    </div>
    <div>
        <dragon msg="You did it!" v-if="menus[0].show"/>
    </div>
</template>
<script setup lang="ts">

import dragon from '../components/dragon.vue'
import {ref} from 'vue'
// import TheWelcome from './components/TheWelcome.vue'
const showBack = ref(false)
const menus = ref([{
    name: 'dragon',
    show: false
}])
const back = ()=>{
    showBack.value=false
    menus.value.forEach(el => {
        el.show = false
    });
}
const show = (menu)=>{
    menu.show = true
    showBack.value=true
}
</script>

<style scoped>
.btn{
    position: fixed;
    left: 20px;
    top: 20px;
}
</style>
