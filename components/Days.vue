<template>
    <div>
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
                    height="300px"
                    width="210px"
                >
                    <template v-slot:title>
                        <span class="text-white font-medium text-2xl lg:text-3xl"> {{ day.day }} </span>
                        <v-skeleton-loader v-if="loading" type="list-item-three-line"></v-skeleton-loader>
                    </template>
                    <template v-slot:subtitle>
                        <span 
                            v-if="!loading"
                            :class="currWeekday === day.day ? 'text-[#48BFE3] font-medium' : isSelected ? 'text-[#4889e3] font-medium' : 'text-white'"
                            class="text-normal text-[16px]"
                        >
                            CP Window
                        </span>
                        <div v-if="!loading">
                            <p class="text-white" v-for="range in day.timeRanges" :key="range.id"> {{ range.window }} </p>
                        </div>
                    </template>
                    <v-card-text>
                        <p v-if="!loading"> click for more info </p>
                    </v-card-text>
                </v-card>
            </v-slide-group-item>
        </v-slide-group>

        <v-expand-transition>
            <v-sheet
                v-if="card != null"
                height="200"
                rounded="lg"
                :elevation="10"
                class="mx-auto w-[90%]"
            >
                <div class="flex align-center justify-center">
                    <h3 class="text-h6">
                        {{ days[card].day }}
                        <v-select
                            v-model="selectedWindow"
                            :items="days[card].timeRanges"
                            item-title="window"
                            variant="outlined"
                            label="Choose time window"
                            width="50vh"
                            @change="console.log('changed')"
                        >
                        </v-select>
                    </h3>
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
        day: 'Monday',
        timeRanges: [
            {window: '7:00am to 10:00am', id: 13},
            {window: '4:00pm to 8:00pm', id: 14}
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

const selectWindow = (e) => {
    console.log(e)
}

const day = new Date()
const currWeekday = days[day.getDay()].day
const loading = false
</script>

<style scoped>
div {
    @apply
        mt-[4vh]
        max-w-[44vh]
        md:max-w-[90vh]
        lg:max-w-[140vh]
}
</style>