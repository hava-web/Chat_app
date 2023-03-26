<template>
    <div class="relative h-10 m-1">
        <div class="grid grid-cols-6" style="border-top: 1px solid #e6e6e6">
            <input v-model="message" 
                    type="text" 
                    name="" 
                    class="col-span-5  p-1 border-none"
                    @keyup.enter="sendMessage()"
                    placeholder="Enter something...">
                    <button
                    @click="sendMessage()"
                    class="place-self-end bg-gray-500 hover:bg-blue-700 p-1 mt-1 rounded text-while">Send</button>
        </div>
    </div>
</template>

<script>
export default {
    props:['room'],
    data() {
        return {
            message: ''
        }
    },
    methods: {
        sendMessage(){
            if(this.message == '')
            {
                return;
            }
            axios.post('/chat/room/' + this.room.id + '/message',{
                message: this.message
            })
            .then(respone => {
                if(respone.status == 201)
                {
                    this.message = '';
                    this.$emit('messagesent');
                }
            })
            .catch(error => {
                console.log(error);
            })
        }
    }
}
</script>