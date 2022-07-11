<script setup>
import {ref} from 'vue';
import HeaderMenuItem from '@/Components/HeaderMenuItem.vue'
defineProps({
    content: Array,
    modelValue: ''
})

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

        <div v-if="showContent && !modelValue" class="absolute -right-[10rem] border bg-white shadow-2xl p-2 rounded-lg min-w-[22rem]">
            <slot name="content"></slot>
        </div>

        <template v-for="i in content" :key="i.id">
            <div v-if="modelValue === i.id" class="absolute -right-[10rem] border bg-white shadow-2xl p-6 rounded-lg min-w-[22rem]">
                <slot :name="i.id"/>
            </div>
        </template>
    </div>
</template>
