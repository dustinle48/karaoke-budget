<template>
    <v-container>
        <div v-if="status=='loading'">
            <h3>Server free nên hơi lâu, chờ xíu!</h3>
        </div>

        <div v-if="status=='error'">
            <h3>Kết nối với server có lỗi!</h3>
        </div>

        <v-data-table
            v-if="status=='success'"
            :headers="headers"
            :items="songs"
            :items-per-page="15"
            :search="search"
            class="elevation-1"
        >
        <template v-slot:top>
            <v-text-field
                v-model="search"
                label="Search"
                append-icon="mdi-magnify"
                single-line
                hide-details
                class="mx-4"
            ></v-text-field>
        </template>
        </v-data-table>
    </v-container>
</template>

<script>
export default {
    data() {
        return {
            status: 'loading',
            songs: [],
            search: '',
        }
    },
    computed: {
        headers() {
            return [
                {
                    text: 'Mã số',
                    align: 'center',
                    value: 'songid'
                },
                {
                    text: 'Tên bài',
                    align: 'center',
                    value: 'name'
                }
            ]
        }
    },
    mounted() {
        this.getAllSong()
    },
    methods: {
        async getAllSong() {
            this.status = 'loading'
            try {
                const songs = await this.$axios.$get('/karaokes')
                this.songs = songs
                this.status = 'success'
            } catch(e) {
                this.status = 'error'
            }
        },
    }
}
</script>

<style>

</style>