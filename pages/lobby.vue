<template>
    <v-container fluid class="text-center">
        <v-row class="main" justify="center" align="center">
            <v-col class="col-lg-2 hidden-md-and-down"></v-col>
            <v-col>
                <v-row>
                    <v-col cols="3" v-for="item in rooms" :key="item.index">
                        <div v-if="status=='loading'">
                            <v-progress-circular
                                indeterminate
                                color="primary"
                            ></v-progress-circular>
                        </div>
                        
                        <div v-if="status=='error'">
                            <h2>An error has occurred!</h2>
                        </div>

                        <div v-if="status=='success'">
                            <v-btn :to="`/room/${item.name}`" height="10rem" color="pink">
                                {{ item.name }}
                            </v-btn>
                        </div>
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
            status: 'loading',
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
        .on('rooms', async (rooms) => {
            this.status = "loading"
            try {
                this.rooms = rooms
                this.status = 'success'
            } catch(e) {
                this.status = 'error'
            }
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