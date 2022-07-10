<script setup>
import {ref} from 'vue';
import HeaderMenuItem from '@/Components/HeaderMenuItem.vue'

const showContent = ref(false)
const toggle = ()=>{
    showContent.value = !showContent.value
}
</script>

<template>
    <div class="relative">
        <header-menu-item @click="toggle">
            <slot name="trigger"/>
        </header-menu-item>

        <div v-if="showContent" class="absolute -right-[10rem] border bg-white shadow-2xl p-2 rounded-lg min-w-[22rem]">
            <slot name="content"></slot>
        </div>
    </div>
</template>
