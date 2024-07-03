<template>
    <div
        class="md:w-[90%] lg:w-[70%] mx-[10px] md:mx-auto"
    >
        <p class="text-2xl my-[16px]"> Calculate travel time </p>
        <div
            class="md:flex md:justify-between md:gap-x-[8px]"
        >
            <v-combobox
                v-model="origin"
                :readonly="loading"
                label="Origin"
                variant="outlined"
                color="#4889e3"
                clearable
                append-inner-icon="mdi-map-marker"
                @click:append-inner="onClickOriginPin"
                hint="Click the pin to get your current location"
                persistent-hint
                class="md:w-[50%] mb-[10px] md:mb-0"
                :items="originLocations"
                :item-props="true"
                @input="onChangeOrigin"
            >
            </v-combobox>
            <v-combobox
                v-model="dest"
                :readonly="loading"
                label="Destination"
                variant="outlined"
                color="#4889e3"
                clearable
                class="md:w-[50%]"
                :items="destLocations"
                :item-props="true"
                append-inner-icon="mdi-map-marker"
                @click:append-inner="onClickDestPin"
                @input="onChangeDest"
            >
            </v-combobox>
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
const config = useRuntimeConfig()
const origin = ref(null)
const dest = ref(null)
const loading = ref(false)
const originLocations = ref([])
const destLocations = ref([])

const handleSubmit = () => {
    console.log(origin.value.value ?? origin.value)
    loading.value = true
    setTimeout(() => (loading.value = false), 2000)
}

const onChangeOrigin = async () => {
    if (origin.value) {
        const response = await fetch('https://places.googleapis.com/v1/places:autocomplete', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'X-Goog-Api-Key': config.public.GCP_API_KEY,
            },
            body: JSON.stringify({
                input: origin.value.value ?? origin.value,
                locationRestriction: {
                    circle: {
                        center: {
                            latitude: 14.570172739611696,
                            longitude: 121.0459622208026
                        },
                        radius: 26000
                    }
                }
            })
        })
        const data = await response.json()

        if (data.suggestions)
            originLocations.value = data.suggestions.map((suggestion) => ({
                title: suggestion.placePrediction.text.text,
                subtitle: suggestion.placePrediction.structuredFormat.secondaryText.text,
            }))
    }
    else {
        originLocations.value = []
    }
}

const onChangeDest = async () => {
    if (dest.value) {
        const response = await fetch('https://places.googleapis.com/v1/places:autocomplete', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
                'X-Goog-Api-Key': config.public.GCP_API_KEY,
            },
            body: JSON.stringify({
                input: dest.value.value ?? dest.value,
                locationRestriction: {
                    circle: {
                        center: {
                            latitude: 14.570172739611696,
                            longitude: 121.0459622208026
                        },
                        radius: 26000
                    }
                }
            })
        })
        const data = await response.json()

        if (data.suggestions)
            destLocations.value = data.suggestions.map((suggestion) => ({
                title: suggestion.placePrediction.text.text,
                subtitle: suggestion.placePrediction.structuredFormat.secondaryText.text,
            }))
    }
    else {
        destLocations.value = []
    }
}

const onClickOriginPin = async () => {
    if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
            async (position) => {
                const response = await fetch(`https://maps.googleapis.com/maps/api/geocode/json?latlng=${position.coords.latitude},${position.coords.longitude}&key=${config.public.GCP_API_KEY}`)
                const data = await response.json()

                console.log(response.ok)
                console.log(data)
                console.log(data.results)
                
                if (response.ok)
                    origin.value = data.results[0].formatted_address
            },
            (err) => {
                console.log(err.message)
            }
        )
    }
    else {
        console.log('Browser does not support geolocation API.')
    }
}

const onClickDestPin = async () => {
    if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
            async (position) => {
                const response = await fetch(`https://maps.googleapis.com/maps/api/geocode/json?latlng=${position.coords.latitude},${position.coords.longitude}&key=${config.public.GCP_API_KEY}`)
                const data = await response.json()
                
                if (response.ok)
                    dest.value = data.results[0].formatted_address
            },
            (err) => {
                console.log(err.message)
            }
        )
    }
    else {
        console.log('Browser does not support geolocation API.')
    }
}
</script>

<style>

</style>