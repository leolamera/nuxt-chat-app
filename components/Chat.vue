<template>
    <div>
        <div class="pt-4 pb-28 space-y-8 overflow-auto ">
            <div class="space-y-8" v-for="(data, idx) in messages" :key="idx">
                <Message :author="data.username" :message="data.message" :botOrigin="data.serverOrigin" />
            </div>
            <div class="fixed bottom-0 left-0">
                <form @submit.prevent="submit" class="flex mx-4 space-x-4">
                    <input v-on:change="changeInput" v-model="message" class="bg-gray-900 py-3 text-white text-xl px-4 w-full my-4  rounded-2xl w-full">
                    <button @click.prevent="submit" class="text-4xl">
                        🧠
                    </button>
                </form>
            </div>
        </div>
    </div>
</template>
<script>
import MessageInput from './MessageInput.vue'
import Message from './Message.vue'

import Pusher from 'pusher-js'
import axios from 'axios'
import { v4 as uuidv4 } from 'uuid';


export default {
    components: [MessageInput, Message],
    data() {
        return {
            username: '#local',
            message: '',
            eventId: '',
            messages: [],
        }
    },
    mounted() {
        
        const eventId = uuidv4()
        this.eventId = eventId
        Pusher.logToConsole = true

        const pusher = new Pusher('3928cda86a47d9009cfe', {
            cluster: 'us2'
        })
        const channel = pusher.subscribe('chat')

        channel.bind(eventId, (data) => {
            data['eventId'] = eventId
            console.log(data)
            this.messages.push(data)
        })
    },
    methods: {
        changeInput() {
            console.log(this.message)
        },
        async submit() {
            const data = {
                username: this.username,
                message: this.message,
                serverOrigin: false,
                eventId: this.eventId

            }
            this.message = ""
            this.messages.push(data)
            await axios({
                method: "POST",
                url: 'https://chatbot-python.us-south.cf.appdomain.cloud/',
                data: data,
                headers: {"Access-Control-Allow-Origin":"*"}
            })
            
            
        }
    }
}
</script>