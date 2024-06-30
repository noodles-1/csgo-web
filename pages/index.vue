<template>
    <div>
        <header>
            <span class="lg:text-5xl md:text-3xl text-2xl font-semibold text-[#48BFE3]"> Automated Congestion Pricing </span>
            <span class="lg:text-2xl text-[20px] text-[#80FFDB]"> Public Information System </span>
        </header>
        
        <Days />
    </div>
</template>

<script>
export default {

}
</script>

<style scoped>
header {
    @apply
        flex
        flex-col
        mt-[6vh]
        ml-[4vh]
        md:mt-[6vh]
        md:items-center
}
</style>