<template>
    <div
        class="md:w-[90%] lg:w-[70%] mx-auto"
    >
        <p class="text-2xl my-[16px]"> Calculate travel time </p>
        <div
            class="md:flex md:justify-between md:gap-x-[8px]"
        >
            <v-text-field
                v-model="origin"
                :readonly="loading"
                label="Origin"
                variant="outlined"
                color="#4889e3"
                clearable
                append-inner-icon="mdi-map-marker"
            >
            </v-text-field>
            <v-text-field
                v-model="dest"
                :readonly="loading"
                label="Destination"
                variant="outlined"
                color="#4889e3"
                clearable
                append-inner-icon="mdi-map-marker"
            >
            </v-text-field>
            <v-btn
                :disabled="!origin || !dest"
                :loading="loading"
                variant="flat"
                size="large"
                class="text-white w-full md:w-[20%]"
                color="#4889e3"
                height="56px"
                @click="handleSubmit"
            >
                Search
            </v-btn>
        </div>
    </div>
</template>

<script setup>
const origin = ref()
const dest = ref()
const loading = ref()

const handleSubmit = () => {
    console.log(origin.value + ' ' + dest.value)
    loading.value = true
    setTimeout(() => (loading.value = false), 2000)
}
</script>

<style>

</style>