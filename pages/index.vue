<template>
    <div>
        <div class="flex justify-center my-[4vh] mb-[6vh] md:hidden">
            <v-icon 
                icon="mdi-eye-circle" 
                color="#48BFE3" 
                size="40" 
            >
            </v-icon>
        </div>
        <header>
            <span class="lg:text-5xl md:text-3xl text-2xl font-semibold text-[#48BFE3]"> Automated Congestion Pricing </span>
            <span class="lg:text-2xl text-[20px] text-[#80FFDB]"> Public Information System </span>
        </header>
        
        <Days />
        <br>
        <v-divider></v-divider>
        <br>
        <Map />
    </div>
</template>

<script>
export default {

}
</script>

<style scoped>
header {
    @apply
        hidden
        md:flex
        md:flex-col
        my-[6vh]
        ml-[4vh]
        md:mt-[6vh]
        md:items-center
}
</style>