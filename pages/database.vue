<template>
    <v-container class="text-center">
        <v-row class="tall">
            <v-col class="hidden-md-and-down"></v-col>
            <v-col>
                <v-tabs class="tall" centered>
                    <v-tab>Dò bài</v-tab>
                    <v-tab>Thêm bài</v-tab>
                    <v-tab to="/">Home</v-tab>

                    <v-tab-item class="songlist">
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
                                    <validation-observer
                                        ref="observer"
                                        v-slot="{ invalid }"
                                    >
                                        <v-form @submit.prevent="addToDatabase">
                                            <validation-provider
                                                v-slot="{ errors }"
                                                name="Song ID"
                                                :rules="{
                                                    required: true,
                                                    digits: 5,
                                                }"
                                            >
                                                <v-text-field outlined label="Mã bài hát" :error-messages="errors" placeholder="Chọn ngẫu nhiên 1 số 5 chữ số" v-on:focusout="checkId" v-model="songId"></v-text-field>
                                            </validation-provider>

                                            <p class="text--disabled">{{ msg }}</p>

                                            <validation-provider
                                                v-slot="{ errors }"
                                                name="Name"
                                                rules="required"
                                            >
                                            <v-text-field outlined label="Tên bài hát" :error-messages="errors" v-model="name"></v-text-field>
                                            </validation-provider>
                                            
                                            <validation-provider
                                                v-slot="{ errors }"
                                                name="Link"
                                                rules="required"
                                            >
                                            <v-text-field outlined label="Link bài hát" :error-messages="errors" v-model="videoId"></v-text-field>
                                            </validation-provider>

                                            <v-btn :disabled="idCheck==true || invalid" type="submit">Thêm</v-btn>
                                        </v-form>
                                    </validation-observer>
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
import { getIdFromUrl } from 'vue-youtube'
import { required, digits, email, max } from 'vee-validate/dist/rules'
import { extend, ValidationObserver, ValidationProvider, setInteractionMode } from 'vee-validate'

setInteractionMode('eager')

extend('digits', {
...digits,
message: '{_field_} needs to be {length} digits. ({_value_})',
})

extend('required', {
...required,
message: '{_field_} can not be empty',
})

extend('max', {
...max,
message: '{_field_} may not be greater than {length} characters',
})

extend('email', {
...email,
message: 'Email must be valid',
})

export default {
    components: {
        ValidationProvider,
        ValidationObserver,
    },
    layout: 'scroll',
    data() {
        return {
            msg: '',
            idCheck: true,
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
                videoid: getIdFromUrl(this.videoId)
                })
                this.count()
                this.name = ''
                this.songId = ''
            } catch(e) {

            }
        },
        async checkId() {
            try {
                await this.$axios.$get(`/karaokes/${this.songId}`)
                this.idCheck = true
                this.msg = 'Trùng ID'
            } catch(e) {
                this.idCheck = false
                this.msg = 'ID hợp lệ'
            }
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