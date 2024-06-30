<template>
    <div class="days-container">
        <v-slide-group
            show-arrows
            center-active
            v-model="card"
        >
            <v-slide-group-item
                v-for="(day, i) in days"
                :key="i"
                v-slot="{ isSelected, toggle }"
            >
                <v-card
                    :variant="(isSelected || currWeekday === day.day) ? 'outlined' : 'plain'"
                    :color="currWeekday === day.day ? '#48BFE3' : isSelected ? '#4889e3' : 'white'"
                    @click="toggle(); selectedWindow = null"
                    height="220px"
                    width="210px"
                    rounded="lg"
                    class="mx-[4px]"
                >
                    <template v-slot:title>
                        <span class="text-white font-medium text-3xl lg:text-3xl"> {{ day.day }} </span>
                        <v-skeleton-loader v-if="loading" type="list-item-three-line"></v-skeleton-loader>
                    </template>
                    <template v-slot:subtitle>
                        <span 
                            v-if="!loading"
                            :class="currWeekday === day.day ? 'text-[#48BFE3] font-medium' : isSelected ? 'text-[#4889e3] font-medium' : 'text-white'"
                            class="text-normal text-[20px]"
                        >
                            CP Window
                        </span>
                        <div v-if="!loading">
                            <p class="text-white text-[16px] md:text-[14px]" v-for="range in day.timeRanges" :key="range.id"> {{ range.window }} </p>
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
                            {{ days[card].day }}
                        </span>
                        <v-select
                            v-if="!loading"
                            v-model="selectedWindow"
                            :items="days[card].timeRanges"
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
                        <div v-if="!loading">
                            <p class="font-medium text-[#48BFE3]"> Detection </p>
                            <p> Cars are detected </p>
                            <p> Motorcycles are detected </p>
                            <p> Buses are detected </p>
                            <p> Trucks are detected </p>
                        </div>
                        <v-skeleton-loader v-if="loading" type="paragraph"></v-skeleton-loader>
                    </div>
                    <div class="w-full md:px-[2vh] lg:px-[10vh] md:flex md:justify-center md:items-center">
                        <div v-if="!loading" class="w-full">
                            <p class="font-medium text-[#48BFE3]"> Charge </p>
                            <div class="flex justify-between">
                                <p> Car </p>
                                <p> PHP 250.00 </p>
                            </div>
                            <div class="flex justify-between">
                                <p> Motorcycle </p>
                                <p> PHP 250.00 </p>
                            </div>
                            <div class="flex justify-between">
                                <p> Bus </p>
                                <p> PHP 250.00 </p>
                            </div>
                            <div class="flex justify-between">
                                <p> Truck </p>
                                <p> PHP 250.00 </p>
                            </div>
                        </div>
                        <v-skeleton-loader v-if="loading" type="paragraph"></v-skeleton-loader>
                    </div>
                </div>
            </v-sheet>
        </v-expand-transition>
    </div>
</template>

<script setup>
const card = defineModel('card')
const selectedWindow = defineModel('selectedWindow')

const days = [
    {
        day: 'Sunday',
        timeRanges: [
            {window: '7:00am to 10:00am', id: 1},
            {window: '4:00pm to 8:00pm', id: 2}
        ]
    },
    {
        day: 'Monday',
        timeRanges: [
            {window: '8:00am to 11:00am', id: 3},
        ]
    },
    {
        day: 'Tuesday',
        timeRanges: [
            {window: '7:00am to 10:00am', id: 5},
            {window: '4:00pm to 8:00pm', id: 6}
        ]
    },
    {
        day: 'Wednesday',
        timeRanges: [
            {window: '7:00am to 10:00am', id: 7},
            {window: '4:00pm to 8:00pm', id: 8}
        ]
    },
    {
        day: 'Thursday',
        timeRanges: [
            {window: '7:00am to 10:00am', id: 9},
            {window: '4:00pm to 8:00pm', id: 10}
        ]
    },
    {
        day: 'Friday',
        timeRanges: [
            {window: '7:00am to 10:00am', id: 11},
            {window: '4:00pm to 8:00pm', id: 12}
        ]
    },
    {
        day: 'Saturday',
        timeRanges: [
            {window: '7:00am to 10:00am', id: 15},
            {window: '4:00pm to 8:00pm', id: 16}
        ]
    },
]

watch(selectedWindow, (newVal, oldVal) => {
    if (!newVal)
        return
    console.log(newVal)
})

const day = new Date()
const currWeekday = days[day.getDay()].day
const loading = false
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