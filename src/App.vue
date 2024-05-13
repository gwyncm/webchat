
    <script>

export default {

    data() {
    return {
            websocket: null,
            connected: false,
            messages: [],
            newMessage: '',
        }
    },
        methods: {
            connectWebSocket() {
                this.websocket = new WebSocket('ws://localhost:3000'); // Change URL to your WebSocket server

                this.websocket.onopen = () => {
                    this.connected = true;
                }

                this.websocket.onmessage = (event) => {
                    event.data.text().then( tx =>{
                        const message = JSON.parse(tx)
                        console.log("msg ",message)
                        this.messages.push(message);
                        })
                }
        
                this.websocket.onclose = () => {
                    this.connected = false;
                    }
                },
            
            sendMessage() {
                if (this.newMessage.trim() !== '') {
                    const message = {
                        user: 'You',
                        text: this.newMessage.trim(),
                    };
                    this.messages.push(message);
                    this.websocket.send(JSON.stringify(message));
                    this.newMessage = '';
                }
            },
        },
        created() {
            this.connectWebSocket();
        },
    }
    </script>

<template>
    <div id="app">
        <h1>WebSocket Chat App</h1>
        <div v-if="connected">
            <div v-for="message in messages" :key="message.id">
                <strong>{{ message.user }}:</strong> {{ message.text }}
            </div>
            <input type="text" v-model="newMessage" @keyup.enter="sendMessage" placeholder="Type your message...">
        </div>
        <div v-else>
            <p>Connecting...</p>
        </div>
    </div>
</template>
