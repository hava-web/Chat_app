<script setup>
import AppLayout from '@/Layouts/AppLayout.vue';
import MessageContainer from '../Chat/messageContainer.vue'
import InputMessage from '../Chat/inputMessage.vue'
import ChatRoomSelection from '../Chat/chatRoomSelection.vue';
</script>

<template>
    <AppLayout title="Dashboard">
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                <ChatRoomSelection 
                    v-if="currentRoom.id"
                    :rooms="chatRooms"
                    :currentRoom="currentRoom"
                    v-on:roomchanged="setRoom($event)"></ChatRoomSelection>
            </h2>
        </template>

        <div class="py-12">
            <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
                <div class="bg-white overflow-hidden shadow-xl sm:rounded-lg">
                    <MessageContainer :messages="messages"></MessageContainer>
                    <InputMessage 
                        :room="currentRoom"
                        v-on:messagesent="getMessage()"></InputMessage>
                </div>
            </div>
        </div>
    </AppLayout>
</template>
<script>
export default {
    data () {
        return {
            chatRooms: [],
            currentRoom: [],
            messages: []
        }
    },
    watch: {
        currentRoom(val, oldVal) {
            if(oldVal.id)
            {
                this.disconnect(oldVal);
            }
            this.connect();
            // console.log(oldVal)
        }
    },  
    methods: {
        connect(){
            if(this.currentRoom.id)
            {
                let vm = this;
                this.getMessage();
                window.Echo.private("chat." + this.currentRoom.id)
                .listen('.message.new',e => {
                    vm.getMessage();
                });
                console.log('Pusher connection established!');
            }
        },
        disconnect(room){
            window.Echo.leave("chat." + room.id);
            console.log('Pusher connection lost!');
        },
        getRooms() {
            axios.get('/chat/rooms')
            .then(respone => {
                this.chatRooms = respone.data;
                this.setRoom(respone.data[0]);
            })
            .catch(error => {
                console.log(error);
            })
        },
        setRoom(room){
            this.currentRoom = room;
        },
        getMessage(){
            axios.get('/chat/room/' + this.currentRoom.id + '/messages')
            .then(respone => {
                this.messages = respone.data;
            })
            .catch(error => {
                console.log(error);
            })
        }
    },
    created() {
        this.getRooms()
    }
}
</script>
