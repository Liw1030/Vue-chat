<script>
export default {
    data() {
        return {
            newMessage: '',
            editMessageText: '',
            editingIndex: -1,
            messages: [],
            firstMessage: [],
            secondMessage: [],
            thirdMessage: [],
            firstResponseTriggered: false,
            secondResponseTriggered: false,
            thirdResponseTriggered: false
        };
    },
    async mounted() {
        await this.loadMessages();
    },
    methods: {
        async loadMessages() {
            const savedMessages = localStorage.getItem('chatMessages');
            if (savedMessages) {
                this.messages = JSON.parse(savedMessages);
            } 
            const response = await fetch('../src/DataChat.json');
            const data = await response.json();
            this.firstMessage = data.firstMessage;
            this.secondMessage = data.secondMessage;
            this.thirdMessage = data.thirdMessage;
        },
        sendMessage() {
            if (this.newMessage.trim() !== '') {
                this.messages.push({ username: 'Tú', avatar: '/public/avatars/image-amyrobson.png', date: 'Now', message: this.newMessage, fromUser: true });
                this.saveMessages();

                if (!this.firstResponseTriggered) {
                    this.triggerFirstMessage();
                    this.firstResponseTriggered = true;
                } else if (this.firstResponseTriggered && !this.secondResponseTriggered) {
                    this.triggerSecondMessage();
                    this.secondResponseTriggered = true;
                } else if (this.firstResponseTriggered && this.secondResponseTriggered && !this.thirdResponseTriggered) {
                    this.triggerThirdMessage();
                    this.thirdResponseTriggered = true;
                }

                this.newMessage = '';
            }
        },
        triggerFirstMessage() {
            this.firstMessage.forEach(msg => this.messages.push({ ...msg, fromUser: false }));
            this.saveMessages();
        },
        triggerSecondMessage() {
            this.secondMessage.forEach(msg => this.messages.push({ ...msg, fromUser: false }));
            this.saveMessages();
        },
        triggerThirdMessage() {
            this.thirdMessage.forEach(msg => this.messages.push({ ...msg, fromUser: false }));
            this.saveMessages();
        },
        saveMessages() {
            localStorage.setItem('chatMessages', JSON.stringify(this.messages));
        },
        replyMessage(username) {
            this.newMessage = `@${username}: `;
            this.$refs.input.focus();
        },
        editMessage(index, message) {
            this.editingIndex = index;
            this.editMessageText = message;
        },
        saveEditMessage(index) {
            if (this.editMessageText.trim() !== '') {
                this.messages[index].message = this.editMessageText;
                this.editingIndex = -1;
                this.editMessageText = '';
                this.saveMessages();
            }
        },
        cancelEditMessage() {
            this.editingIndex = -1;
            this.editMessageText = '';
        },
        deleteMessage(index) {
            this.messages.splice(index, 1);
            this.saveMessages();
        }
    }
};
</script>

<template>
    <div class="content">
        <div>
            <div class="chat-window">
                <div v-for="(msg, index) in messages" :key="index" class="message">
                    <div class="image-container">
                        <img :src="msg.avatar" :alt="msg.username" />
                    </div>
                    <div class="message-info">
                        <div class="message-header">
                            {{ msg.username }} <p class="date">{{ msg.date }}</p>
                            <button class="reply" @click="replyMessage(msg.username)" v-if="!msg.fromUser">Reply</button>
                            <button class="edit" @click="editMessage(index, msg.message)" v-if="msg.username === 'Tú'">Edit</button>
                            <button class="remove" @click="deleteMessage(index)" v-if="msg.username === 'Tú'">Delete</button>
                        </div>
                        <div class="message-body">
                            <div v-if="editingIndex === index">
                                <input v-model="editMessageText" />
                                <button class="save" @click="saveEditMessage(index)">Save</button>
                                <button class="cancel" @click="cancelEditMessage()">Cancel</button>
                            </div>
                            <div v-else>
                                {{ msg.message }}
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="send-message">
                <img class="image-input" src="/public/avatars/image-amyrobson.png" alt="user" />
                <input v-model="newMessage" placeholder="Add comment..." ref="input" />
                <button class="send" @click="sendMessage">Send</button>
            </div>
        </div>
    </div>
</template>

<style>
.content {
    display: flex;
    justify-content: center;
    align-items: center;
    margin: 50px 0 50px 0;
}

.chat-window {
    width: 545px;
    height: 400px;
    margin-bottom: 10px;
    overflow-y: auto;
}

.message {
    margin-bottom: 10px;
    box-sizing: border-box;
    background-color: #fff;
    color: black;
    border-radius: 5px;
    width: auto;
    height: auto;
    display: flex;
}

.image-container {
    margin: 10px 10px 10px 10px;
}

.message-info {
    flex: 1;
}

.message-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 5px;
    margin-top: 10px;
}

.message-body {
    margin-top: 5px;
    margin-bottom: 10px;
}

input {
    width: 100%;
    padding: 10px;
    box-sizing: border-box;
    border: 1px solid hsl(210, 7%, 59%);
    border-radius: 5px;
}

img {
    border-radius: 50px;
    width: 30px;
    height: 30px;
}

.send-message {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px;
    border-top: 1px solid #ccc;
    background-color: #f9f9f9;
    border-radius: 5px;
}

.remove {
    background: url(/public/icon-delete.svg) no-repeat 10px center;
    color: red;
    border: none;
    background-color: transparent;
    cursor: pointer;
    padding-left: 25px;
    margin-right: 10px;
}

.reply {
    background: url(/public/icon-reply.svg) no-repeat 10px center;
    color: rgb(13, 0, 128);
    border: none;
    background-color: transparent;
    cursor: pointer;
    padding-left: 30px;
    margin-right: 10px;
}

.edit {
    background: url(/public/icon-edit.svg) no-repeat 10px center;
    color: rgb(13, 0, 128);
    border: none;
    background-color: transparent;
    cursor: pointer;
    padding-left: 30px;
    margin-right: -200px;
}

.save {
    color: green;
    margin-left: 5px;
    background-color: transparent;
    border: 1px solid green;
    border-radius: 5px;
}

.cancel {
    color: red;
    margin-right: 10px;
    background-color: transparent;
    border: 1px solid red;
    border-radius: 5px;
    margin-left: 5px;
}

p.date {
    color: hsl(211, 10%, 45%);
    margin-left: -180px;
}

.send {
    background-color: hsl(238, 40%, 52%);
    color: white;
    border: none;
    border-radius: 5px;
    margin-left: 10px;
    height: 25px;
}

.text {
    color: black;
}

.image-input {
    margin-right: 10px;
}
</style>
