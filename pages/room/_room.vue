<template>
    <v-container fluid>
        <div>
            <v-menu
                class="menu"
                :close-on-content-click="false"
                offset-x
            >
            <template v-slot:activator="{ on, attrs }">
                <v-btn
                rounded
                v-bind="attrs"
                v-on="on"
                class="menu-btn"
                >
                    <v-icon>mdi-post</v-icon>
                </v-btn>
            </template>

            <div class="controller text-center">
                <v-row>
                <!-- Bên trái -->
                <v-col cols="4">
                    <v-row>
                        <v-col>
                            <v-progress-circular
                                v-if="status=='loading'"
                                indeterminate
                                color="primary"
                            ></v-progress-circular>
                            <div v-else>
                                <v-text-field outlined label="Mã bài hát" v-model="songIdToAdd" clearable></v-text-field>
                                <p v-if="msg" class="text--disabled">{{ msg }}</p>
                                <v-btn @click="addSongToList" :disabled="!songIdToAdd">Thêm</v-btn>
                                <v-btn @click="addSongToTop" :disabled="!songIdToAdd">Lên đầu</v-btn>
                                <v-btn @click="nextSong">Next</v-btn>
                            </div>
                            <v-btn class="ma-6" to="/"><v-icon>mdi-home</v-icon></v-btn>
                        </v-col>
                    </v-row>
                </v-col>

                <!-- Bên phải -->
                <v-col cols="8">
                    <div v-if="songList.length" class="border">
                    <v-row>
                        <v-col cols="3"><v-icon>mdi-play</v-icon> Đang phát</v-col>
                        <v-col cols="9">{{ songList[0].name }}</v-col>
                    </v-row>
                    </div>
                    <div v-if="songList.length >= 2" class="mt-4 scroll">
                    <v-row>
                        <v-col cols="3"><v-icon>mdi-playlist-music</v-icon>Bài tiếp</v-col>
                        <v-col cols="7">{{ songList[1].name }}</v-col>
                        <v-col cols="2">
                        <v-btn elevation="0" fab x-small @click="deleteSongFromList(1)"><v-icon>mdi-trash-can-outline</v-icon></v-btn>
                        </v-col>
                    </v-row>
                    <v-row v-for="(item, index) in songList.slice(2,songList.length)" :key="item.index">
                        <v-col cols="3"></v-col>
                        <v-col cols="7">{{ item.name }}</v-col>
                        <v-col cols="2">
                        <v-btn elevation="0" fab x-small @click="topSongFromList(item, index+2)"><v-icon>mdi-arrow-up-bold-circle-outline</v-icon></v-btn>
                        <v-btn elevation="0" fab x-small @click="deleteSongFromList(index+2)"><v-icon>mdi-trash-can-outline</v-icon></v-btn>
                        </v-col>
                    </v-row>
                    </div>
                </v-col>
                </v-row>
            </div>
            </v-menu>

            <div class="text-center" v-if="songList.length == 0">
                <h1>Chọn bài đi, đừng ngại</h1>
                <h1>Bấm F11 để mở full màn hình</h1>
            </div>

            <client-only>
                <div class="youtube" v-if="songList.length > 0">
                    <youtube :video-id="songList[0].videoid" :player-vars="{ autoplay: 1 }" ref="youtube" @ended="end"></youtube>
                </div>
            </client-only>
        </div>
    </v-container>
</template>

<script>
import Vue from 'vue'
import VueYouTube from 'vue-youtube'
Vue.use(VueYouTube)

export default {
    head: {
        title: 'Karaoke',
        meta: [
            {
                hid: 'Karaoke',
                name: 'Karaoke',
                content: 'Karaoke'
            }
        ],
    },
    data() {
        return {
            status: '',
            songList: [],
            songId: '',
            name: '',
            videoId: '',
            songIdToAdd: '',
            msg: '',
            room: this.$route.params.room
        }
    },
    mounted() {
        this.socket = this.$nuxtSocket({
            name: 'main',
            channel: '/'
        })
        this.socket.emit('connect-to-room', this.room)
        /* --------------------------------------- */
        this.socket
        .on('user-connected', (user) => {
            this.socket.emit('song-list', user, this.songList)
        })
        .on('user-disconnect', (user, room) => {
            this.chat.push(`${user} left ${room}`)
        })
        .on('add-song-to-list', (song, user) => {
            this.songList.push(song)
        })
        .on('add-song-to-top', (song) => {
            this.songList.splice(1,0,song)
        })
        .on('top-song-from-list', (song,index) => {
            this.topSongFromList(song,index)
        })
        .on('delete-song-from-list',(index) =>{
            this.deleteSongFromList(index)
        })
        .on('next-song', () => {
            this.nextSong()
        })
    },
    computed: {
        player() {
            return this.$refs.youtube.player
        }
    },
    watch: {
        songList: function() {
            this.socket.emit('new-song-list', this.room, this.songList)
        }
    },
    methods: {
        end() {
            this.songList.shift()
        },

        nextSong() {
            this.songList.shift()
        },

        async addSongToList() {
            this.status = 'loading'
            try {
                const song = await this.$axios.$get(`/karaokes/${this.songIdToAdd}`)
                this.songIdToAdd = ''
                this.msg = 'Đã thêm bài vào list'
                this.status = 'success'
                this.songList.push(song)
            } catch(e) {
                this.status = 'error'
                this.msg = 'Bài hát không tồn tại'
                this.songIdToAdd = ''
            }
        },
        async addSongToTop() {
            this.status = 'loading'
            try {
                const song = await this.$axios.$get(`/karaokes/${this.songIdToAdd}`)
                this.songIdToAdd = ''
                this.msg = 'Đã thêm bài hát lên đầu!'
                this.status = 'success'
                this.songList.splice(1,0,song)
            } catch(e) {
                this.status = 'error'
                this.msg = 'Bài hát không tồn tại'
                this.songIdToAdd = ''
            }
        },

        topSongFromList(song,index) {
            this.songList.splice(index,1)
            this.songList.splice(1,0,song)
        },
        deleteSongFromList(index) {
            this.songList.splice(index,1)
        }
    }
}
</script>

<style scoped>
.container {
    height: 100vh;
    padding: 0;
}
.menu-btn {
    z-index: 24;
    position: fixed;
    top: 2rem;
    left: 2rem;
}
.controller {
    background-color: wheat;
    z-index: 24;
    top: 3rem;
    left: 3rem;
    width: 55rem;
    height: 20rem;
    padding: 0.5rem;
}
.scroll {
    height: 16rem;
    overflow-y: scroll;
    overflow-x: hidden;
}
.border {
    border: 1px black solid;
}
</style>
