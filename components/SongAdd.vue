<template>
    <v-container>
        <div class="mt-6">
            <v-progress-circular
                v-if="status=='loading'"
                indeterminate
                color="primary"
            ></v-progress-circular>

            <v-row v-if="status=='success'">
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
                                <v-text-field outlined label="Mã bài hát" :error-messages="errors" placeholder="Chọn ngẫu nhiên 1 số 5 chữ số" v-on:focusout="checkId" v-model="songId" clearable></v-text-field>
                            </validation-provider>

                            <p class="text--disabled">{{ idmsg }}</p>

                            <validation-provider
                                v-slot="{ errors }"
                                name="Name"
                                rules="required"
                            >
                            <v-text-field outlined label="Tên bài hát" :error-messages="errors" v-model="name" clearable></v-text-field>
                            </validation-provider>
                            
                            <validation-provider
                                v-slot="{ errors }"
                                name="Link"
                                rules="required"
                            >
                            <v-text-field outlined label="Link bài hát" :error-messages="errors" v-model="videoId" clearable></v-text-field>
                            </validation-provider>

                            <p class="text--disabled">{{ videoidmsg }}</p>

                            <v-btn :disabled="idCheck==true || invalid" type="submit">Thêm</v-btn>
                            <v-btn @click="clear">Xóa</v-btn>
                        </v-form>
                    </validation-observer>
                </v-col>
            </v-row>
        </div>
    </v-container>
</template>

<script>
import swal from 'sweetalert'
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
    data() {
        return {
            status: 'success',
            idCheck: true,
            videoidCheck: true,
            name: '',
            songId: '',
            videoId: '',
            idmsg: '',
            videoidmsg: '',
        }
    },
    methods: {
        clear() {
            this.songId = ''
            this.name = ''
            this.videoId = ''
        },
        async addToDatabase() {
            this.status = 'loading'
            try {
                this.$axios.$post('/karaokes', {
                    name: this.name,
                    songid: this.songId,
                    videoid: getIdFromUrl(this.videoId)
                })
                this.name = ''
                this.songId = ''
                this.videoId = ''
                this.status = 'success'
                swal({
                    title: "Success!",
                    text: "Song added successfully",
                    icon: "success",
                });
            } catch(e) {
                swal({
                    title: "Oh no!",
                    text: "Error has occured!",
                    icon: "error",
                });
                this.status = 'error'
            }
        },
        async checkId() {
            this.idmsg = 'Checking ...'
            try {
                await this.$axios.$get(`/karaokes/${this.songId}`)
                this.idCheck = true
                this.idmsg = 'Duplicate ID'
            } catch(e) {
                this.idCheck = false
                this.idmsg = 'Valid ID'
            }
        },
        async checkVideoId() {
            this.videoidmsg = 'Checking ...'
            let link = getIdFromUrl(this.videoId)
            try {
                await this.$axios.$get(`/karaokes/${link}`)
                this.videoidCheck = true
                this.videoidmsg = 'Duplicate link'
            } catch(e) {
                this.videoidCheck = false
                this.videoidmsg = 'Valid link'
            }
        },
    }
}
</script>

<style>

</style>