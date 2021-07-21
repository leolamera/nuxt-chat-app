<template>
    <div>
        <div class="flex justify-end pt-4 pb-28 space-y-8">
            <div class="fixed h-5/6 w-full md:max-w-4xl">
                <ul v-chat-scroll class="space-y-8 overflow-auto max-h-full" >
                    <li v-for="(data, idx) in messages" :key="idx">
                        <Message :author="data.username" :message="data.message" :botOrigin="data.serverOrigin" />
                    </li>
                </ul>
            </div>
            
            


            <div class="md:flex md:justify-end md:w-full fixed bottom-0 left-0">
                <form @submit.prevent="submit" class="md:bg-blue-100 px-4 md:px-20 md:w-5/12">
                    <div class="flex max-h-full space-x-4 justify-between">
                        <input v-on:change="changeInput" v-model="message" class="bg-gray-900 py-3 text-white text-xl px-4 w-full my-4  rounded-2xl">
                        <button @click.prevent="submit" class="text-4xl">
                            🧠
                        </button>
                    </div>
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