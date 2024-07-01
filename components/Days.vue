<template>
    <div class="days-container">
        <div class="text-2xl md:text-center md:text-4xl mx-[4vh] lg:mx-[8vh] my-[4vh]">
            <span> Today is </span>
            <span class="font-medium"> {{ currWeekday }}. </span>
        </div>
        <v-slide-group
            show-arrows
            center-active
            v-model="card"
        >
            <v-slide-group-item
                v-for="day in days"
                :key="day"
                v-slot="{ isSelected, toggle }"
            >
                <v-card
                    :variant="(isSelected || currWeekday === day) ? 'outlined' : 'plain'"
                    :color="currWeekday === day ? '#48BFE3' : isSelected ? '#4889e3' : 'white'"
                    @click="toggle(); selectedWindow = null"
                    height="220px"
                    width="210px"
                    rounded="lg"
                    class="mx-[4px]"
                >
                    <template v-slot:title>
                        <span class="text-white font-medium text-3xl lg:text-3xl"> {{ day }} </span>
                        <v-skeleton-loader v-if="loading" type="list-item-three-line"></v-skeleton-loader>
                    </template>
                    <template v-slot:subtitle>
                        <span 
                            v-if="!loading"
                            :class="currWeekday === day ? 'text-[#48BFE3] font-medium' : isSelected ? 'text-[#4889e3] font-medium' : 'text-white'"
                            class="text-normal text-[20px]"
                        >
                            CP Window
                        </span>
                        <div v-if="!loading && daysResponse[day].length == 0">
                            <p class="text-white text-[16px] md:text-[14px]"> (none) </p>
                        </div>
                        <div v-if="!loading && daysResponse[day].length > 0">
                            <p class="text-white text-[16px] md:text-[14px]" v-for="dayResponse in daysResponse[day].slice(0, 3)" :key="dayResponse.id"> {{ dayResponse.window }} </p>
                            <div v-if="daysResponse[day].length > 3">
                                <p class="text-white text-[16px] md:text-[14px]"> (more) </p>
                            </div>
                        </div>
                    </template>
                    <v-card-text>
                        <p v-if="!loading" class="text-[16px] md:text-[14px]"> click for more info </p>
                    </v-card-text>
                </v-card>
            </v-slide-group-item>
        </v-slide-group>

        <v-expand-transition class="my-[4vh]">
            <v-sheet
                v-if="card != null"
                :elevation="10"
                class="mx-auto lg:w-[90%] md:h-[160px] lg:h-[210px] rounded-lg"
            >
                <div class="p-[4vh] md:p-[0vh] flex flex-col md:flex-row md:items-center justify-between md:justify-around h-full">
                    <div class="md:px-[2vh] lg:px-[10vh]">
                        <span class="text-2xl font-semibold">
                            {{ days[card] }}
                        </span>
                        <v-select
                            v-if="!loading"
                            v-model="selectedWindow"
                            :items="daysResponse[days[card]]"
                            item-title="window"
                            item-value="id"
                            variant="outlined"
                            label="Choose time window"
                            class="mt-[16px] w-full md:w-[30vh]"
                            color="#48BFE3"
                        >
                        </v-select>
                        <v-skeleton-loader v-if="loading" type="ossein" class="h-[40px]"></v-skeleton-loader>
                    </div>
                    <div class="w-full mb-[2vh] md:mb-[0vh] md:px-[0vh] lg:px-[5vh] md:flex md:justify-center md:items-center lg:border-r-[1px] border-gray-600">
                        <div v-if="!windowLoading && selectedWindow && setting">
                            <p class="font-medium text-[#48BFE3]"> Detection </p>
                            <p> Cars are <span v-if="!setting.detectCar"> not </span> detected </p>
                            <p> Motorcycles are <span v-if="!setting.detectMotorcycle"> not </span> detected </p>
                            <p> Buses are <span v-if="!setting.detectBus"> not </span> detected </p>
                            <p> Trucks are <span v-if="!setting.detectTruck"> not </span> detected </p>
                        </div>
                        <v-skeleton-loader v-if="windowLoading && selectedWindow" type="paragraph"></v-skeleton-loader>
                    </div>
                    <div class="w-full md:px-[2vh] lg:px-[10vh] md:flex md:justify-center md:items-center">
                        <div v-if="!windowLoading && selectedWindow && setting" class="w-full">
                            <p class="font-medium text-[#48BFE3]"> Charge </p>
                            <div class="flex justify-between">
                                <p> Car </p>
                                <p> PHP {{ setting.carPrice }} </p>
                            </div>
                            <div class="flex justify-between">
                                <p> Motorcycle </p>
                                <p> PHP {{ setting.motorcyclePrice }} </p>
                            </div>
                            <div class="flex justify-between">
                                <p> Bus </p>
                                <p> PHP {{ setting.busPrice }} </p>
                            </div>
                            <div class="flex justify-between">
                                <p> Truck </p>
                                <p> PHP {{ setting.truckPrice }} </p>
                            </div>
                        </div>
                        <v-skeleton-loader v-if="windowLoading && selectedWindow" type="paragraph"></v-skeleton-loader>
                    </div>
                </div>
            </v-sheet>
        </v-expand-transition>
    </div>
</template>

<script setup>
import moment from 'moment-timezone'

const card = defineModel('card')
const selectedWindow = defineModel('selectedWindow')

const currWeekday = moment().tz("Asia/Manila").format('dddd')

const days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday']
const daysResponse = ref(null)
const loading = ref(true)
const windowLoading = ref(true)
const setting = ref(null)
const config = useRuntimeConfig()

watch(selectedWindow, async (newVal, _) => {
    if (!newVal)
        return

    windowLoading.value = true
    try {
        const response = await fetch(`${config.public.SERVER_URL}/settings/${newVal}`)
        const data = await response.json()
        setting.value = data
        windowLoading.value = false
    }
    catch (err) {
        windowLoading.value = true
    }
})

const toDate = (timeStr) => {
    const now = new Date()
    const nowDateTime = now.toISOString()
    const nowDate = nowDateTime.split('T')[0]
    return new Date(nowDate + 'T' + timeStr)
}

const to12HourFormat = (timeStr) => {
    const date = toDate(timeStr)
    return date.toLocaleString('en-US', {
        hour: 'numeric',
        minute: 'numeric',
        hour12: true
    })
}

const fetchData = async () => {
    daysResponse.value = []
    loading.value = true

    try {
        const fetchPromises = days.map(async function(day) {
            const response = await fetch(`${config.public.SERVER_URL}/settings/day/${day}`)
            const data = await response.json()
            data.sort((a, b) => {
                return toDate(a.hourFrom) - toDate(b.hourFrom)
            })
            daysResponse.value[day] = data
            daysResponse.value[day].forEach(dayResponse => {
                dayResponse.window = to12HourFormat(dayResponse.hourFrom) + ' to ' + to12HourFormat(dayResponse.hourTo)
            })
        })

        await Promise.all(fetchPromises)
        loading.value = false
    }
    catch (err) {
        loading.value = true
        // show error message
    }
}

await fetchData()
</script>

<style scoped>
.days-container {
    @apply
        mt-[4vh]
        max-w-[44vh]
        md:max-w-[90vh]
        lg:max-w-[140vh]
}
</style>