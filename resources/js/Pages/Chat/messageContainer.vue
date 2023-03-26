<script setup>
import MessageItem from '../Chat/messageItem.vue'
</script>
<template>
    <div class="h-96 w-full">
        <div class="h-full p-2 flex flex-col-reverse overflow-scroll">
            <div class="" v-for="(message,index) in messages" :key="index">
                <MessageItem :message="message"></MessageItem>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props: ['messages']
}
</script>