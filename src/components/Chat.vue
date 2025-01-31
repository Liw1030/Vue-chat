<script>
export default {
    data() {
        return {
            newMessage: '',
            editMessageText: '',
            editingIndex: -1,
            messages: []
        };
    },
    mounted() {
        this.loadMessages();
    },
    methods: {
        sendMessage() {
            if (this.newMessage.trim() !== '') {
                this.messages.push({ user: 'Tú', text: this.newMessage });
                this.saveMessages();
                this.newMessage = '';
            }
        },
        saveMessages() {
            localStorage.setItem('chatMessages', JSON.stringify(this.messages));
        },
        loadMessages() {
            const savedMessages = localStorage.getItem('chatMessages');
            if (savedMessages) {
                this.messages = JSON.parse(savedMessages);
            }
        },
        replyMessage(user) {
            this.newMessage = `@${user}: `;
            this.$refs.input.focus();
        },
        editMessage(index, text) {
            this.editingIndex = index;
            this.editMessageText = text;
        },
        saveEditMessage(index) {
            if (this.editMessageText.trim() !== '') {
                this.messages[index].text = this.editMessageText;
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
                <div class="message">
                    <div class="image-container">
                        <img src="/public/avatars/image-maxblagun.png" alt="Chrystian" />
                    </div>
                    <div class="message-info">
                        <div class="message-header">
                            Chrystian <p class="date">1 week ago</p>
                            <button class="reply" @click="replyMessage('Chrystian')">Reply</button>
                        </div>
                        <div class="message-body">
                            ¡Hola! ¿Cómo estás?
                        </div>
                    </div>
                </div>

                <div class="message">
                    <div class="image-container">
                        <img src="/public/avatars/image-ramsesmiron.png" alt="Miller" />
                    </div>
                    <div class="message-info">
                        <div class="message-header">
                            Miller <p class="date">1 week ago</p>
                            <button class="reply" @click="replyMessage('Miller')">Reply</button>
                        </div>
                        <div class="message-body">
                            ¡Hola! Todo bien, ¿y tú?
                        </div>
                    </div>
                </div>

                <div class="message">
                    <div class="image-container">
                        <img src="/public/avatars/image-ramsesmiron.png" alt="Miller" />
                    </div>
                    <div class="message-info">
                        <div class="message-header">
                            Miller <p class="date">1 week ago</p>
                            <button class="reply" @click="replyMessage('Miller')">Reply</button>
                        </div>
                        <div class="message-body">
                            La tarea de hoy es terminar el chat.
                        </div>
                    </div>
                </div>

                <div v-for="(msg, index) in messages" :key="index" class="message-me">
                    <div class="image-container">
                        <img src="/public/avatars/image-amyrobson.png" alt="user" />
                    </div>
                    <div class="message-info">
                        <div v-if="editingIndex === index">
                            <div class="message-header">
                                <input v-model="editMessageText" />
                                <button class="save" @click="saveEditMessage(index)">Save</button>
                                <button class="cancel" @click="cancelEditMessage()">Cancel</button>
                            </div>
                        </div>
                        <div v-else>
                            <div class="message-header">
                                Tú <p class="date">Now</p>
                                <button class="edit" @click="editMessage(index, msg.text)">Editar</button>
                                <button class="remove" @click="deleteMessage(index)">Delete</button>
                            </div>
                            <div class="message-body">
                                <p class="text">{{ msg.user }}: {{ msg.text }}</p>
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
}

.chat-window {
    width: 500px;
    height: auto;
    padding: 10px;
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

.message-me {
    margin-bottom: 10px;
    box-sizing: border-box;
    background-color: #fff;
    color: black;
    border-radius: 5px;
    width: auto;
    height: auto;
    margin-left: 30px;
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

.text{
    color: black;
}

.image-input{
    margin-right: 10px;
}
</style>
