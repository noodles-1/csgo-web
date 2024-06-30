<template>
    <v-app>
        <div class="main-container">
            <slot />
        </div>
    </v-app>
</template>

<style scoped>
.main-container {
    @apply
        md:max-w-[90vh]
        lg:max-w-[140vh]
        mx-auto
        flex
        flex-col
        items-center
        mb-[10vh]
}
</style>