<template>
    <div>
        <div class="flex flex-col justify-end h-80">
            <div ref="messagesContainer" class="p-4 overflow-y-auto max-h-fit">
                <div
                    v-for="message in messages"
                    :key="message.id"
                    class="flex items-center mb-2"
                >
                    <div
                        v-if="message.sender_id === currentUser.id"
                        class="p-2 ml-auto text-white bg-blue-500 rounded-lg"
                    >
                        {{ message.text }}
                    </div>
                    <div v-else class="p-2 mr-auto bg-gray-200 rounded-lg">
                        {{ message.text }}
                    </div>
                </div>
            </div>
        </div>
        <div class="flex items-center">
            <input
                type="text"
                v-model="newMessage"
                @keydown="sendTypingEvent"
                @keyup.enter="sendMessage"
                placeholder="Type a message..."
                class="flex-1 px-2 py-1 border rounded-lg"
            />
            <button
                @click="sendMessage"
                class="px-4 py-1 ml-2 text-white bg-blue-500 rounded-lg"
            >
                Send
            </button>
        </div>
        <small v-if="isFriendTyping" class="text-gray-700">
            {{ friend.name }} is typing...
        </small>
    </div>
</template>

<script>
import axios from "axios";

export default {
    props: {
        friend: {
            type: Object,
            required: true,
        },
        currentUser: {
            type: Object,
            required: true,
        },
    },
    data() {
        return {
            messages: [],
            newMessage: "",
            messagesContainer: null,
            isFriendTyping: false,
            isFriendTypingTimer: null,
        };
    },
    watch: {
        messages: {
            handler() {
                this.$nextTick(() => {
                    this.messagesContainer.scrollTo({
                        top: this.messagesContainer.scrollHeight,
                        behavior: "smooth",
                    });
                });
            },
            deep: true,
        },
    },
    methods: {
        sendMessage() {
            if (this.newMessage.trim() !== "") {
                axios
                    .post(`/messages/${this.friend.id}`, {
                        message: this.newMessage,
                    })
                    .then((response) => {
                        this.messages.push(response.data);
                        this.newMessage = "";
                    });
            }
        },
        sendTypingEvent() {
            Echo.private(`chat.${this.friend.id}`).whisper("typing", {
                userID: this.currentUser.id,
            });
        },
    },
    mounted() {
        axios.get(`/messages/${this.friend.id}`).then((response) => {
            console.log(response.data);
            this.messages = response.data;
        });

        Echo.private(`chat.${this.currentUser.id}`)
            .listen("MessageSent", (response) => {
                this.messages.push(response.message);
            })
            .listenForWhisper("typing", (response) => {
                this.isFriendTyping = response.userID === this.friend.id;

                if (this.isFriendTypingTimer) {
                    clearTimeout(this.isFriendTypingTimer);
                }

                this.isFriendTypingTimer = setTimeout(() => {
                    this.isFriendTyping = false;
                }, 1000);
            });
    },
};
</script>