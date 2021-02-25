<template>
    <v-container fluid class="text-center">
        <v-row class="main" justify="center" align="center">
            <v-col class="col-lg-2 hidden-md-and-down"></v-col>
            <v-col>
                <v-row>
                    <v-col cols="3" v-for="item in rooms" :key="item.index">
                        <v-btn :to="`/room/${item.name}`" height="10rem" color="pink">
                            {{ item.name }}
                        </v-btn>
                    </v-col>
                </v-row>
            </v-col>
            <v-col class="col-lg-2 hidden-md-and-down"></v-col>
        </v-row>
    </v-container>
</template>

<script>
export default {
    data() {
        return {
            rooms: [],
            color: ['blue','red','green','cyan']
        }
    },
    mounted() {
        this.socket = this.$nuxtSocket({
            name: 'main',
            channel: '/'
        })
        /* --------------------------------------- */
        this.socket
        .on('rooms', (rooms) => {
            this.rooms = rooms
        })
    },
    methods: {
    }
}
</script>

<style scoped>
.container {
    height: 100vh;
}
.main {
    min-height: 100vh;
}
.v-btn {
    width: 100%;
}
</style>