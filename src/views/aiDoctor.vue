<template>

  <div class="chat-container">
    <div class="chat-box">
      <div v-for="(message, index) in messages" :key="index" class="message">
        <div :class="{'user-message': message.role === 'user', 'bot-message': message.role === 'bot'}">
          {{ message.text }}
        </div>
      </div>
    </div>
    <div class="input-box">
      <input v-model="userInput" @keyup.enter="sendMessage" placeholder="请输入您的消息..." />
      <button @click="sendMessage">发送</button>
    </div>
  </div>
</template>

<script>
// import axios from 'axios';
import request from "@/utils/request";

export default {
  data() {
    return {
      userInput: '',
      messages: []
    };
  },
  methods: {
    async sendMessage() {
      if (this.userInput.trim() === '') return;

      // 添加用户消息到消息列表
      this.messages.push({ role: 'user', text: this.userInput });

      try {
        // 向后端发送请求
        const response = await request.post('api/chat', {
          message: this.userInput
        });

        // 添加返回的消息到消息列表
        this.messages.push({ role: 'bot', text: response.data.reply });

        // 清空输入框
        this.userInput = '';
      } catch (error) {
        console.error('Error sending message:', error);
      }
    }
  }
};
</script>

<style scoped>
.chat-container {
  width: 100%;
  max-width: 600px;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  height: 80vh;
  border: 1px solid #ccc;
  border-radius: 10px;
}

.chat-box {
  flex: 1;
  padding: 10px;
  overflow-y: auto;
}

.message {
  margin-bottom: 10px;
}

.user-message {
  text-align: right;
  background-color: #daf1da;
  padding: 10px;
  border-radius: 10px;
}

.bot-message {
  text-align: left;
  background-color: #f1f1f1;
  padding: 10px;
  border-radius: 10px;
}

.input-box {
  display: flex;
  border-top: 1px solid #ccc;
  padding: 10px;
}

input {
  flex: 1;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

button {
  margin-left: 10px;
  padding: 10px 15px;
  border: none;
  background-color: #007bff;
  color: white;
  border-radius: 5px;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3;
}
</style>
