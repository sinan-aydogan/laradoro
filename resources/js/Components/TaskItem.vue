<script setup>
defineProps({
    status: Boolean,
    task: Object
})
</script>
<template>
    <div
        class="flex justify-between w-full items-center border p-4 rounded-xl hover:shadow"
         :class="[
             {'bg-slate-100': task.status}
         ]"
    >
        <!--Task Info-->
        <div class="flex items-center space-x-2">
            <!--Checkbox-->
            <input @change="$emit('update:status', $event.target.checked)" type="checkbox" :checked="task.status"  class="rounded-full w-5 h-5 cursor-pointer checked:bg-slate-300"/>

            <!--Todo title-->
            <span v-text="task.title" :class="[
                {'line-through text-slate-300': task.status},
            ]"></span>
        </div>

        <!--Actions-->
        <div class="flex space-x-2">
            <!--Delete-->
            <div class="flex bg-slate-100 hover:bg-rose-100 w-8 h-8 items-center justify-center text-slate-500 hover:text-rose-500 cursor-pointer rounded-full">
                <font-awesome-icon icon="trash"/>
            </div>

            <!--Edit-->
            <div class="flex bg-slate-100 hover:bg-sky-100 w-8 h-8 items-center justify-center text-slate-500 hover:text-sky-500 cursor-pointer rounded-full">
                <font-awesome-icon icon="pen"/>
            </div>
        </div>
    </div>
</template>
