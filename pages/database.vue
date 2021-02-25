<template>
    <v-container class="text-center">
        <v-row class="tall">
            <v-col class="hidden-md-and-down"></v-col>
            <v-col>
                <v-tabs class="tall" centered>
                    <v-tab>Control</v-tab>
                    <v-tab>Search</v-tab>
                    <v-tab>Add</v-tab>
                    <v-tab to="/">Home</v-tab>

                    <v-tab-item>
                        <div v-if="!room">
                            <v-row>
                                <v-col cols="3" v-for="item in rooms" :key="item.index">
                                    <v-btn class="my-3" @click="connectToRoom(item.name)">
                                        {{ item.name }}
                                    </v-btn>
                                </v-col>
                            </v-row>
                        </div>
                        <div v-if="room">
                            <h2 class="my-3">Connected to {{ room }}</h2>
                        </div>
                        <div v-if="room">
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
                                                        <v-col class="text-left" cols="1"><v-icon>mdi-play</v-icon></v-col>
                                                        <v-col class="text-center" cols="7">{{ songList[0].name }}</v-col>
                                                        <v-col cols="3"></v-col>
                                                    </v-row>
                                                </div>
                                                <div v-if="songList.length >= 2" class="mt-4 scroll">
                                                    <v-row>
                                                        <v-col class="text-left" cols="1"><v-icon>mdi-playlist-music</v-icon></v-col>
                                                        <v-col class="text-center" cols="7">{{ songList[1].name }}</v-col>
                                                        <v-col class="text-right" cols="4">
                                                            <v-btn elevation="0" fab x-small @click="deleteSongFromList(1)"><v-icon>mdi-trash-can-outline</v-icon></v-btn>
                                                        </v-col>
                                                    </v-row>
                                                    <v-row v-for="(item, index) in songList.slice(2,songList.length)" :key="item.index">
                                                        <v-col class="text-left" cols="1"></v-col>
                                                        <v-col class="text-center" cols="7">{{ item.name }}</v-col>
                                                        <v-col class="text-right" cols="4">
                                                            <v-btn elevation="0" fab x-small @click="moveSongUp(item, index+2)"><v-icon>mdi-chevron-up</v-icon></v-btn>
                                                            <v-btn elevation="0" fab x-small @click="topSongFromList(item, index+2)"><v-icon>mdi-chevron-triple-up</v-icon></v-btn>
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
                    </v-tab-item>
                    <v-tab-item class="ma-3">
                        <SongList />
                    </v-tab-item>

                    <v-tab-item class="ma-3">
                        <SongAdd />
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
            rooms: [],
            connected: false,
            room: '',
            chat: [],
            status: '',
            songList: [],
            songId: '',
            name: '',
            videoId: '',
            songIdToAdd: '',
            msg: '',
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
        /* --------------------------------------- */
        this.socket
        .on('user-connected', (room, user) => {
            this.chat.push(`${user} joined ${room}`)
        })
        .on('user-disconnected', (room, user) => {
            this.chat.push(`${user} left ${room}`)
        })
        .on('song-list', (songList) => {
            this.songList = songList
        })
        .on('add-song-to-list', (song, user) => {
            this.chat.push(`${user} added ${song.name}`)
        })
        .on('add-song-to-top', (song, user) => {
            this.chat.push(`${user} topped ${song.name}`)
        })
        .on('move-song-up', (song, index, user) => {
            this.chat.push(`${user} moved ${song.name} up from ${index}`)
        })
        .on('top-song-from-list', (song, index, user) => {
            this.chat.push(`${user} topped ${song.name} from ${index}`)
        })
        .on('delete-song-from-list',(index, user) => {
            this.chat.push(`${user} delete a song in ${index}`)
        })
        .on('next-song', (user) => {
            this.chat.push(`${user} next current song`)
        })
    },
    methods: {
        connectToRoom(room) {
            this.songList = ''
            if (!this.room) {
                this.room = room
                this.socket.emit('connect-to-room', room)
                this.connected = this.socket.connected
            } else {
                this.socket.emit('disconnect-from-room', this.room)
                this.socket.emit('connect-to-room', room)
                this.room = room
                this.connected = this.socket.connected
            }
        },

        nextSong() {
            this.socket.emit('next-song', this.room)
        },

        async addSongToList() {
            this.status = 'loading'
            try {
                const song = await this.$axios.$get(`/karaokes/${this.songIdToAdd}`)
                this.songIdToAdd = ''
                this.msg = 'Đã thêm bài vào list'
                this.status = 'success'
                this.socket.emit('add-song-to-list', this.room, song)
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
                this.socket.emit('add-song-to-top', this.room, song)
            } catch(e) {
                this.status = 'error'
                this.msg = 'Bài hát không tồn tại'
                this.songIdToAdd = ''
            }
        },
        moveSongUp(song, index) {
            this.socket.emit('move-song-up', this.room, song, index)
        },
        topSongFromList(song, index) {
            this.socket.emit('top-song-from-list', this.room, song, index)
        },
        deleteSongFromList(index) {
            this.socket.emit('delete-song-from-list', this.room, index)
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
.border {
    border: 1px black solid;
}
</style>