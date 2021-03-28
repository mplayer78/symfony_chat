<template>
<div class="chat-container">
  <h1 class="chat-header">
    Vue Chat
  </h1>
  <div class="chat-box">
    <div class="chat-item" v-for="(message, index) of messages" :key="index">
      <span class="chat-item__name">
        {{ message.userName }}
      </span>
      <span class="chat-item__message">
        {{ message.message }}
      </span>
    </div>
  </div>
</div>
  <div class="chat-input">
    <form action="post">
      <label for="name">Name:</label>
      <input type="text" id="name" v-model="userName">
      <label for="message">Message:</label>
      <input type="text" id="message" v-model="message">
      <button id="btnSubmit" @click.prevent="handleSubmit">Go!</button>
    </form>
  </div>
</template>

<script>
export default {
  name: 'chat',
  data() {
    return {
      userName: '',
      message: '',
      socket: null,
      messages: [],
    };
  },
  mounted() {
    this.socket = new WebSocket('ws://localhost:3001');

    this.socket.addEventListener('open', () => console.log('Connected'));

    this.socket.addEventListener('message', (e) => {
      this.handleReceive(JSON.parse(e.data));
      console.log(this.messages);
    });
  },
  computed: {
    msgObj() {
      return {
        userName: this.userName,
        message: this.message,
      };
    },
  },
  methods: {
    handleSubmit() {
      this.socket.send(JSON.stringify(this.msgObj));
      this.handleReceive(this.msgObj);
    },
    handleReceive(msg) {
      this.messages.push(msg);
    },
  },
};
</script>

<style scoped>
  h1 {
    border: solid red 2px
  }
</style>
