<template>
    <v-container>
        <div>
            <v-tabs centered>
                <v-tab>Controller</v-tab>
                <v-tab>Activity</v-tab>

                <v-tab-item>
                    <div class="controller text-center">
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
                        <v-row>
                            <v-col>
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
                </v-tab-item>

                <v-tab-item>
                    <Chat :chat="chat"/>
                </v-tab-item>
            </v-tabs>     
        </div>
    </v-container>
</template>

<script>
export default {
    data() {
        return {
            status: '',
            songList: [],
            songId: '',
            name: '',
            videoId: '',
            songIdToAdd: '',
            msg: '',
            chat: [],
        }
    },
    mounted() {
        this.socket = this.$nuxtSocket({
            name: 'main',
            channel: '/'
        })
        /* --------------------------------------- */
        this.socket
        .on('user-connected', (user, room) => {
            this.chat.push(`${user} joined ${room}`)
        })
        .on('user-disconnected', (user, room) => {
            this.chat.push(`${user} left ${room}`)
        })
        .on('song-list', (songList) => {
            this.songList = songList
        })
        .on('add-song-to-list', (song, user) => {
            this.chat.push(`${user} added ${song.name}`)
        })
        .on('add-song-to-top', (song) => {
            this.chat.push(`${user} topped ${song.name}`)
        })
        .on('top-song-from-list', (song,index) => {
            this.topSongFromList(song,index)
        })
        .on('delete-song-from-list',(index) =>{
            this.deleteSongFromList(index)
        })
    },
    computed: {
        player() {
            return this.$refs.youtube.player
        }
    },
    watch: {
    },
    methods: {
        nextSong() {
            this.socket.emit('next-song')
        },

        async addSongToList() {
            this.status = 'loading'
            try {
                const song = await this.$axios.$get(`/karaokes/${this.songIdToAdd}`)
                this.songIdToAdd = ''
                this.msg = 'Đã thêm bài vào list'
                this.status = 'success'
                this.socket.emit('add-song-to-list', song)
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
                this.socket.emit('add-song-to-top', song)
            } catch(e) {
                this.status = 'error'
                this.msg = 'Bài hát không tồn tại'
                this.songIdToAdd = ''
            }
        },

        topSongFromList(song, index) {
            this.socket.emit('top-song-from-list', song, index)
        },
        deleteSongFromList(index) {
            this.socket.emit('delete-song-from-list', index)
        }
    }
}
</script>

<style scoped>
.container {
    padding: 0;
}
.controller {
    height: 100%;
    background-color: wheat;
    padding: 0.5rem;
}
</style>