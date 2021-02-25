<template>
    <v-container>
        <p v-for="item in chat" :key="item.index">{{ item }}</p>
    </v-container>
</template>

<script>
export default {
    props: ['chat'],
    data() {
        return {
        }
    }
}
</script>

<style>

</style>