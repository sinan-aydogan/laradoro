<script setup>
import { computed, ref } from 'vue'
import HeaderMenuItem from '@/Components/HeaderMenuItem.vue'
import TaskItem from '@/Components/TaskItem.vue'

/*Task functions*/
const tasks = ref([
    {
        title: 'Task 1',
        status: false
    },
    {
        title: 'Task 2',
        status: false
    },
    {
        title: 'Task 3',
        status: false
    }
])
const activeList = computed(() => {
    return tasks.value.sort((a, b) => (a.status - b.status))
})
const activeTaskCount = computed(() => {
    return tasks.value.filter(i => i.status === false).length
})
const showAddForm = ref(false)
const taskForm = ref({
    title: '',
    status: false
})
const addTaskItem = () => {
    let validate = () => {
        return !tasks.value.find(i => i.title.toLowerCase() === taskForm.value.title.toLowerCase())
    }
    if (taskForm.value.title && validate()) {
        tasks.value.push({
            title: taskForm.value.title,
            status: taskForm.value.status
        })
        taskForm.value.title = ''
        showAddForm.value = false
    } else {
        alert('Lütfen benzersiz bir görev ekleyin')
    }
}
const updateTaskItem = (task) => {
    if (task[0] === 'status') {
        tasks.value.find(i => i.title === task[1].title).status = task[2]
    } else {
        tasks.value.find(i => i.title === task[1].title).title = task[2]
    }
}
const deleteTaskItem = (task) => {
    let del = () => {
        let index = tasks.value.findIndex(i => i.title === task.title)
        tasks.value.splice(index, 1)
    }
    confirm('Görev silinecek. Emin misiniz?') ? del() : null
}

/*Timer functions*/
const levels = ref({
    baby: {
        pomodoro: 10,
        shortRest: 5,
        longRest: 10
    },
    popular: {
        pomodoro: 20,
        shortRest: 5,
        longRest: 15
    },
    medium: {
        pomodoro: 40,
        shortRest: 8,
        longRest: 20
    },
    extended: {
        pomodoro: 60,
        shortRest: 10,
        longRest: 25
    },
    custom: {
        pomodoro: 15,
        shortRest: 5,
        longRest: 10
    }
})
</script>

<template>
    <div class="h-screen w-screen overflow-hidden">
        <!--Header-->
        <div class="flex justify-between px-4 py-6">
            <!--Logo-->
            <div>
                <span class="text-2xl font-bold">Laradoro</span>
            </div>

            <!--Feature-->
            <div class="flex space-x-2">
                <!--Customize-->
                <header-menu-item>
                    <font-awesome-icon icon="sliders"/>
                    <span>Özelleştir</span>
                </header-menu-item>

                <!--Reset Timer-->
                <header-menu-item>
                    <font-awesome-icon icon="arrow-rotate-left"/>
                    <span>Oturumu Sıfırla</span>
                </header-menu-item>

                <!--Language Changer-->
                <header-menu-item>
                    <font-awesome-icon icon="globe"/>
                    <span>Dili Değiştir</span>
                </header-menu-item>
            </div>
        </div>

        <!--Content-->
        <div class="flex h-full justify-between divide-x">
            <!--Timer-->
            <div class="flex flex-col items-center h-full w-full px-6">
                <!--Header-->
                <span class="font-bold text-2xl">Sıkı bir çalışmaya ne dersin? 🚀</span>

                <!--Tabs-->
                <div class="flex mt-16 mb-10 font-semibold border rounded-full">
                    <!--Pomodoro-->
                    <div class="border-r px-4 py-2">Pomodoro</div>
                    <!--Rest-->
                    <div class="border-r px-4 py-2">Rest</div>
                    <!--Long Rest-->
                    <div class="px-4 py-2">Long Reset</div>
                </div>

                <!--Timer-->
                <div
                    class="flex flex-col justify-center items-center border-8 border-indigo-300 text-indigo-600 rounded-full h-80 w-80 mb-10">
                    <span class="text-8xl mt-8 mb-12 font-bold font-sans">15:00</span>
                    <span class="text-xl">Level</span>
                    <span class="font-bold text-x">Custom</span>
                </div>

                <!--Activity Button-->
                <button class="bg-indigo-500 text-white px-8 py-2 space-x-4 text-3xl rounded-full">
                    <font-awesome-icon icon="play"/>
                    <span>Başlat</span>
                </button>
            </div>

            <!--Todo-->
            <div class="flex flex-col items-center w-[32rem] px-6">
                <!--Header-->
                <div class="flex w-full justify-between items-center mb-5">

                    <!--Title-->
                    <div class="flex items-center space-x-2">
                        <span class="text-3xl font-semibold">Görevlerim</span>
                        <span class="flex justify-center items-center text-xl bg-indigo-200 w-8 h-8 rounded-full"
                              v-text="activeTaskCount"></span>
                    </div>

                    <!--Actions-->
                    <div
                        class="flex justify-center items-center h-10 w-10 rounded-full bg-slate-100 hover:bg-indigo-50 text-slate-400 hover:text-indigo-500 active:border-2 border-indigo-200 cursor-pointer transition duration-200">
                        <font-awesome-icon icon="ellipsis" size="lg"/>
                    </div>
                </div>

                <!--Tasks-->
                <div class="flex flex-col space-y-2 w-full">
                    <!--Task-->
                    <template v-for="i in activeList" :key="i.title">
                        <task-item
                            :task="i"
                            v-model:status="tasks.find(j=>j.title === i.title).status"
                            @update="updateTaskItem($event)"
                            @delete="deleteTaskItem($event)"
                        />
                    </template>
                </div>

                <!--Add Task-->
                <div class="flex flex-col w-full">
                    <!--Add Button-->
                    <button v-if="!showAddForm" @click="showAddForm = true"
                            class="flex h-12 border-2 text-emerald-600 hover:text-emerald-700 hover:scale-[1.03] active:scale-[0.98] border-emerald-600 hover:bg-emerald-100 space-x-2 border-dashed justify-center items-center rounded-xl my-4 transition">
                        <font-awesome-icon icon="plus"/>
                        <span class="text-lg font-semibold"> Yeni görev ekle</span>
                    </button>

                    <!--Add Text Input-->
                    <div
                        v-if="showAddForm"
                        class="border border-slate-600 rounded-xl p-4 my-4"
                    >
                        <input type="text" @keydown.enter="addTaskItem" v-model="taskForm.title"
                               class="flex w-full border-none focus:ring-0 px-2 text-lg"
                               placeholder="Görevinizi buraya yazın..."/>
                        <div class="space-x-2">
                            <!--Save-->
                            <button @click="addTaskItem" class="bg-emerald-500 text-white px-2 py-1 rounded-lg">Listeye
                                Ekle
                            </button>
                            <!--Cancel-->
                            <button @click="showAddForm=false"
                                    class="hover:bg-amber-100 hover:text-amber-600 px-2 py-1 rounded-md">İptal Et
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
