<script setup>
import {computed, onMounted, ref} from 'vue'
import HeaderMenuItem from '@/Components/HeaderMenuItem.vue'
import TaskItem from '@/Components/TaskItem.vue'
import DropDown from '@/Components/DropDown.vue'
import SwitchInput from "@/Components/SwitchInput.vue";

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
        label: 'Yeni Başlayan',
        pomodoro: 10,
        shortRest: 5,
        longRest: 10
    },
    popular: {
        label: 'Popüler',
        pomodoro: .1,
        shortRest: 5,
        longRest: 15
    },
    medium: {
        label: 'Orta',
        pomodoro: 40,
        shortRest: 8,
        longRest: 20
    },
    extended: {
        label: 'Gelişmiş',
        pomodoro: 60,
        shortRest: 10,
        longRest: 25
    },
    custom: {
        label: 'Özel',
        pomodoro: 15,
        shortRest: 5,
        longRest: 10
    }
})
const activeLevel = ref('popular')

const counter = ref({
    timer: levels.value[activeLevel.value].pomodoro * 60,
    isRunning: false,
})

const minute = computed(() => {
    let i = Math.floor(counter.value.timer / 60)
    return i > 9 ? i : '0' + i
})

const second = computed(() => {
    let i = Math.floor(counter.value.timer % 60)

    return i > 9 ? i : '0' + i
})

const startTimer = () => {
    counter.value.isRunning = true
    let fn = setInterval(() => {
        counter.value.timer--
        if (counter.value.timer === 0) {
            clearInterval(fn)
        }
    }, 1000)
};

/*Customize menu*/
const activeCustomizeLink = ref('')
const customizeMenuLinks = ref([
    {
        id: 'level',
        label: 'Çalışma Modları',
        subLabel: 'Farklı modları seçebilirsiniz',
        icon: 'clock'
    },
    {
        id: 'alarm',
        label: 'Alarm',
        subLabel: 'Alarm seslerini değiştirebilirsiniz',
        icon: 'bell'
    },
    {
        id: 'auto',
        label: 'Otomatik Başlama',
        subLabel: 'Çalışmadan molaya otomatik geçiş',
        icon: 'arrow-rotate-right'
    },
    {
        id: 'notify',
        label: 'Bildirimler',
        subLabel: 'Tarayıcınızdan bildirim alın',
        icon: 'message'
    }
])

/*Alarm*/
const alarm = ref({
    sound: 'bip',
    volume: 50
})
const alarms = [
    {
        id: 'bird',
        label: 'Kuş Sesi',
        file: 'alarm_bird.mp3'
    },
    {
        id: 'bip',
        label: 'Bipleme',
        file: 'alarm_beep.mp3'
    },
    {
        id: 'can',
        label: 'Çan Sesi',
        file: 'alarm_bell.mp3'
    },
    {
        id: 'mute',
        label: 'Sessiz',
    }
]
const audioChannel = ref(null)

onMounted(() => {
    audioChannel.value = new Audio()
})

const playSound = (sound, volume) => {
    if(sound) {
        audioChannel.value.src = sound
        audioChannel.value.volume = volume
        audioChannel.value.play()
    }
}

/*Auto*/
const autoStart = ref({
    pomodoro: true,
    rest: true
})

/*Notify*/
const notify = ref(false)

/*Reset*/
const reset = () => {
    let resetAll = () => {
        tasks.value = []
        counter.value.timer = levels.value[activeLevel.value].pomodoro * 60
        counter.value.isRunning = false
        activeLevel.value = 'popular'
        alarm.value.sound = 'bip'
        alarm.value.volume = 50
        autoStart.value.pomodoro = true
        autoStart.value.rest = true
        notify.value = false
    }
    confirm('Oturumu ve seçenekleri sıfırlamak istediğinize emin misiniz?') ? resetAll() : null
}
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
                <drop-down :content="customizeMenuLinks" v-model="activeCustomizeLink">
                    <template #trigger>
                        <font-awesome-icon icon="sliders"/>
                        <span>Özelleştir</span>
                    </template>
                    <template #content>
                        <div class="space-y-4">
                            <template v-for="i in customizeMenuLinks">
                                <div @click="activeCustomizeLink = i.id"
                                     class="flex justify-between w-full items-center px-6 py-2 hover:bg-indigo-50 rounded-lg group cursor-pointer transition duration-200">
                                    <div class="flex items-center space-x-4">
                                        <!--Icon-->
                                        <font-awesome-icon :icon="i.icon" size="lg"
                                                           class="text-slate-700 group-hover:text-indigo-600"/>
                                        <!--Label-->
                                        <div class="flex flex-col">
                                            <span v-text="i.label"
                                                  class="font-semibold text-slate-700 group-hover:text-indigo-600"></span>
                                            <span v-text="i.subLabel"
                                                  class="text-xs text-slate-500 group-hover:text-indigo-600"></span>
                                        </div>
                                    </div>
                                    <!--Trigger Icon-->
                                    <div>
                                        <font-awesome-icon icon="chevron-right"/>
                                    </div>
                                </div>
                            </template>
                        </div>
                    </template>

                    <template #level>
                        <!--Header-->
                        <div @click="activeCustomizeLink=''"
                             class="flex space-x-4 items-center mb-4 text-slate-700 cursor-pointer">
                            <font-awesome-icon icon="arrow-left-long" size="lg"/>
                            <h2 class="text-xl font-bold">Çalışma modunu özelleştir</h2>
                        </div>

                        <!--Content-->
                        <div class="flex flex-col space-y-4">
                            <template v-for="(i,index) in levels">
                                <div class="flex space-x-2 items-center">
                                    <input type="radio" class="w-6 h-6" :id="index" :value="index" name="active-level"
                                           v-model="activeLevel"/>
                                    <div>
                                        <span v-text="i.label" class="font-bold"/>
                                        <div>
                                            <span v-text="i.pomodoro"
                                                  class="text-slate-400 after:content-['dk\00a0•\00a0']"/>
                                            <span v-text="i.shortRest"
                                                  class="text-slate-400 after:content-['dk\00a0•\00a0']"/>
                                            <span v-text="i.longRest" class="text-slate-400 after:content-['dk']"/>
                                        </div>
                                    </div>
                                </div>
                            </template>
                        </div>

                    </template>
                    <template #alarm>
                        <!--Header-->
                        <div @click="activeCustomizeLink=''"
                             class="flex space-x-4 items-center mb-4 text-slate-700 cursor-pointer">
                            <font-awesome-icon icon="arrow-left-long" size="lg"/>
                            <h2 class="text-xl font-bold">Alarmı özelleştir</h2>
                        </div>

                        <!--Content-->
                        <div class="flex flex-col space-y-4">
                            <div>
                                <span class="flex font-bold mb-2">Alarm melodisi</span>
                                <div class="flex justify-center">
                                    <template v-for="i in alarms">
                                        <span @click="playSound(i.id !== 'mute' ? '/sounds/'+alarms.find(j=>j.id === i.id).file : '', alarm.volume/100); alarm.sound = i.id"
                                              v-text="i.label"
                                              class="flex flex-grow border border-r-0 last:border-r first:rounded-l-lg last:rounded-r-lg p-2 cursor-pointer hover:bg-blue-600 active:bg-blue-700 hover:text-white transition duration-200"
                                              :class="[{
                                                'bg-blue-600 text-white': i.id === alarm.sound,
                                            }]"
                                        ></span>
                                    </template>
                                </div>
                            </div>
                            <div>
                                <div class="flex justify-between">
                                    <span class="font-bold">Ses seviyesi</span>
                                    <span class="font-bold after:content-['%']" v-text="alarm.volume"></span>
                                </div>
                                <div class="flex w-full">
                                    <input type="range" v-model="alarm.volume" class="flex w-full py-4"/>
                                </div>
                            </div>
                        </div>

                    </template>
                    <template #auto>
                        <!--Header-->
                        <div @click="activeCustomizeLink=''"
                             class="flex space-x-4 items-center mb-4 text-slate-700 cursor-pointer">
                            <font-awesome-icon icon="arrow-left-long" size="lg"/>
                            <h2 class="text-xl font-bold">Sayaç başlatmayı özelleştir</h2>
                        </div>

                        <!--Content-->
                        <div class="flex flex-col space-y-2 text-slate-700 font-bold mt-8">
                            <!--Start pomodoro-->
                            <div class="flex w-full justify-between items-center">
                                <span>Çalışmayı otomatik başlat</span>
                                <switch-input v-model="autoStart.pomodoro"/>
                            </div>
                            <!--Start rest-->
                            <div class="flex w-full justify-between items-center">
                                <span>Molayı otomatik başlat</span>
                                <switch-input v-model="autoStart.rest"/>
                            </div>
                        </div>

                    </template>
                    <template #notify>
                        <!--Header-->
                        <div @click="activeCustomizeLink=''"
                             class="flex space-x-4 items-center mb-4 text-slate-700 cursor-pointer">
                            <font-awesome-icon icon="arrow-left-long" size="lg"/>
                            <h2 class="text-xl font-bold">Bildirimi özelleştir</h2>
                        </div>

                        <!--Content-->
                        <div class="flex flex-col space-y-2 text-slate-700 font-bold mt-8">
                            <!--Start pomodoro-->
                            <div class="flex w-full justify-between items-center">
                                <span>Bittiğinde tarayıcı bildirimi göster</span>
                                <switch-input v-model="notify"/>
                            </div>
                        </div>

                    </template>
                </drop-down>

                <!--Reset Timer-->
                <header-menu-item @click="reset">
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
                    class="flex flex-col justify-center items-center border-8 border-indigo-200 text-indigo-600 rounded-full h-[25rem] w-[25rem] mb-10">
                    <div class="text-[7rem] mt-4 mb-6 font-semibold font-sans">
                        <span v-text="minute" class="after:content-[':']"></span>
                        <span v-text="second"></span>
                    </div>
                    <span class="text-xl">Mod</span>
                    <span class="font-bold text-xl" v-text="levels[activeLevel].label"></span>
                </div>

                <!--Activity Button-->
                <button
                    @click="startTimer"
                    class=" text-white px-8 py-2 space-x-4 text-3xl rounded-full"
                    :class="[
                        {
                            'bg-rose-500': counter.isRunning,
                            'bg-indigo-500': !counter.isRunning
                        }
                    ]"
                >
                    <font-awesome-icon :icon="counter.isRunning ? 'pause' : 'play'"/>
                    <span v-text="counter.isRunning ? 'Durdur' : 'Başlat'"></span>
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
