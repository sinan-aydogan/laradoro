<script setup>
import { onMounted, ref, toRefs } from 'vue'

const props = defineProps({
    status: Boolean,
    task: Object
})
const {task} = toRefs(props)

const showEditForm = ref(false)
const updateForm = ref({})
onMounted(()=>{
    updateForm.value = {
        title: task.value.title,
        status: task.value.status
    }
})
</script>
<template>
    <!--Task Item-->
    <div
        v-if="!showEditForm"
        class="flex justify-between w-full items-center border p-4 rounded-xl hover:shadow"
         :class="[
             {'bg-slate-100': task.status}
         ]"
    >
        <!--Task Info-->
        <div class="flex items-center space-x-2">
            <!--Checkbox-->
            <input
                @change="$emit('update',['status',task,$event.target.checked])"
                type="checkbox"
                :checked="task.status"
                class="rounded-full w-5 h-5 cursor-pointer checked:bg-slate-300"
            />

            <!--Todo title-->
            <span v-text="task.title" :class="[
                {'line-through text-slate-300': task.status},
            ]"></span>
        </div>

        <!--Actions-->
        <div class="flex space-x-2">
            <!--Delete-->
            <div @click="$emit('delete', task)" class="flex bg-slate-100 hover:bg-rose-100 w-8 h-8 items-center justify-center text-slate-500 hover:text-rose-500 cursor-pointer rounded-full">
                <font-awesome-icon icon="trash"/>
            </div>

            <!--Edit-->
            <div @click="showEditForm=true" class="flex bg-slate-100 hover:bg-sky-100 w-8 h-8 items-center justify-center text-slate-500 hover:text-sky-500 cursor-pointer rounded-full">
                <font-awesome-icon icon="pen"/>
            </div>
        </div>
    </div>

    <!--Edit Form-->
    <div
        v-else
        class="border border-slate-600 rounded-xl p-4 my-4"
    >
        <input type="text" @keydown.enter="$emit('update',['title', task, updateForm.title])" v-model="updateForm.title" class="flex w-full border-none focus:ring-0 px-2 text-lg" placeholder="Görevinizi buraya yazın..."/>
        <div class="space-x-2">
            <!--Save-->
            <button @click="$emit('update',['title', task, updateForm.title])" class="bg-emerald-500 text-white px-2 py-1 rounded-lg">Görevi Güncelle</button>
            <!--Cancel-->
            <button @click="showEditForm=false" class="hover:bg-amber-100 hover:text-amber-600 px-2 py-1 rounded-md">İptal Et</button>
        </div>
    </div>
</template>
