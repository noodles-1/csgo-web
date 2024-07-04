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
        <div v-if="distance && actualDuration && trafficLevel " class="bg-gray-800 h-[100px] md:h-[60px] flex justify-around md:grid md:grid-cols-3 items-center rounded-t-lg mt-[20px]"> 
            <div class="text-[16px] text-center md:border-r-[1px] border-gray-600 flex flex-col md:block">
                <span class="font-medium"> Distance: </span> <span> {{ distance }} </span>
            </div>
            <div class="text-[16px] text-center md:border-r-[1px] border-gray-600 flex flex-col md:block">
                <span class="font-medium"> Duration </span> <span class="font-medium"> (actual): </span> <span> {{ actualDuration }} </span>
            </div>
            <div class="text-[16px] text-center flex flex-col md:block">
                <span class="font-medium"> Traffic level: </span> <span> {{ trafficLevel }} </span> 
            </div>
        </div>
        <div 
            id="googleMap"
            class="w-full h-[500px] border-[#4889e3]"
            :class="distance ? 'rounded-b-lg' : 'rounded-lg mt-[20px]'"
        >
        </div>
    </div>
</template>

<script setup>
import { Loader } from '@googlemaps/js-api-loader'
import { ref } from 'vue'

const config = useRuntimeConfig()
const origin = ref(null)
const dest = ref(null)
const loading = ref(false)
const originLocations = ref([])
const destLocations = ref([])
const distance = ref(null)
const actualDuration = ref(null)
const trafficLevel = ref(null)

const loader = new Loader({
    apiKey: config.public.GCP_API_KEY,
    version: 'weekly',
})

const mapOptions = {
    center: {
        lat: 14.570172739611696,
        lng: 121.0459622208026
    },
    zoom: 10,
    backgroundColor: '#121212',
    mapTypeControl: false,
    fullscreenControl: false,
    minZoom: 8,
    maxZoom: 20,
    streetViewControl: false
}

var map
var directionsService
var directionsRenderer

async function loadMap() {
    const { Map } = await loader.importLibrary('maps')
    map = new Map(document.getElementById('googleMap'), mapOptions)

    const { DirectionsService, DirectionsRenderer } = await loader.importLibrary('routes')
    directionsService = new DirectionsService()
    directionsRenderer = new DirectionsRenderer()
    directionsRenderer.setMap(map)
}

loadMap()

const handleSubmit = async () => {
    const originValue = origin.value.value ?? origin.value
    const destValue = dest.value.value ?? dest.value
    
    const request = {
        origin: originValue,
        destination: destValue,
        travelMode: 'DRIVING',
        unitSystem: 0,
        provideRouteAlternatives: true,
        drivingOptions: {
            departureTime: new Date(),
            trafficModel: 'pessimistic'
        }
    }

    directionsService.route(request, (res, status) => {
        if (status == 'OK') {
            directionsRenderer.setDirections(res)
            const durationValue = res.routes[0].legs[0].duration.value
            const actualDurationValue = res.routes[0].legs[0].duration_in_traffic.value
            console.log(res.routes[0].legs[0].duration.text)
            console.log(res.routes[0].legs[0].duration_in_traffic.text)

            distance.value = res.routes[0].legs[0].distance.text
            actualDuration.value = res.routes[0].legs[0].duration_in_traffic.text
            const traffic = Number(durationValue / actualDurationValue).toFixed(2)
            
            if (traffic < 0.7)
                trafficLevel.value = 'heavy traffic'
            else if (traffic < 0.9)
                trafficLevel.value = 'moderate traffic'
            else
                trafficLevel.value = 'light traffic'
        }
        else
            directionsRenderer.setDirections({ routes: [] })
    })
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
                        radius: 32000
                    }
                }
            })
        })
        const data = await response.json()

        if (data.suggestions)
            originLocations.value = data.suggestions.map((suggestion) => ({
                title: suggestion.placePrediction.structuredFormat.mainText.text,
                subtitle: suggestion.placePrediction.structuredFormat.secondaryText.text,
                value: suggestion.placePrediction.text.text
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
                title: suggestion.placePrediction.structuredFormat.mainText.text,
                subtitle: suggestion.placePrediction.structuredFormat.secondaryText.text,
                value: suggestion.placePrediction.text.text
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