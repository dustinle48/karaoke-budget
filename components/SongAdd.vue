<template>
    <v-container>
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
                                <v-text-field outlined label="Mã bài hát" :error-messages="errors" placeholder="Chọn ngẫu nhiên 1 số 5 chữ số" v-on:focusout="checkId" v-model="songId" clearable></v-text-field>
                            </validation-provider>

                            <p class="text--disabled">{{ msg }}</p>

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
            status: 'loading',
            idCheck: true,
            name: '',
            songId: '',
            videoId: '',
            msg: '',
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
                this.status = 'success'
            } catch(e) {
                this.status = 'error'
            }
        },
        async checkId() {
            this.msg = 'Đang check, chờ xíu ...'
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

<style>

</style>