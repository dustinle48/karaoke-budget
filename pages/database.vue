<template>
    <v-container class="text-center">
        <v-row class="tall">
            <v-col class="hidden-md-and-down"></v-col>
            <v-col>
                <v-tabs class="tall" centered>
                    <v-tab>Dò bài</v-tab>
                    <v-tab>Thêm bài</v-tab>
                    <v-tab to="/">Home</v-tab>

                    <v-tab-item>
                        <v-data-table
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
                    </v-tab-item>

                    <v-tab-item class="tall">
                        <div class="adder mt-6">
                            <v-row>
                                <v-col>
                                    <v-form @submit.prevent="addToDatabase">
                                        <v-text-field outlined label="Mã bài hát" disabled v-model="songId"></v-text-field>
                                        <v-text-field outlined label="Tên bài hát" v-model="name"></v-text-field>
                                        <v-text-field outlined label="Link bài hát" v-model="videoId"></v-text-field>
                                        <v-btn type="submit">Thêm</v-btn>
                                        <v-btn @click="count">Đếm</v-btn>
                                    </v-form>
                                </v-col>
                            </v-row>
                        </div>
                    </v-tab-item>
                </v-tabs>
            </v-col>
            <v-col class="hidden-md-and-down"></v-col>
        </v-row>
    </v-container>
</template>

<script>
export default {
    data() {
        return {
            search: '',
            songs: [],
            name: '',
            songId: '',
            videoId: '',
        }
    },
    mounted() {
        this.getAllSong()
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
    methods: {
        async getAllSong() {
            try {
                const songs = await this.$axios.$get('/karaokes')
                this.songs = songs
            } catch(e) {

            }
        },
        async addToDatabase() {
            try {
                this.$axios.$post('/karaokes', {
                name: this.name,
                songid: this.songId,
                videoid: getIdFromURL(this.videoId)
                })
                this.name = ''
                this.songId = ''
            } catch(e) {

            }
        },
        async count() {
            this.songId = await this.$axios.$get('/karaokes/count') + 1
        },
    }
}
</script>

<style scoped>
.container {
    height: 100vh;
}
.tall {
    height: 100vh;
}
</style>