<template>
  <div>
    <button @click="sendUpdateNotification">
      模擬送貼文
    </button>
    <P>{{ message }}</P>
  </div>
</template>


<script setup>
import { ref, computed, onUnmounted } from "vue";
import { io } from "socket.io-client";
const host = 'http://localhost:3005';
const token = '';

const notificationCount = ref(0);

const socket = io(host, {
  path: "/socket.io/connect",
  transports: ['websocket'],
  autoConnect: true,                //启动自从自动连接
  secure: true,
  transports: ['websocket'],        // ['websocket', 'polling']
  reconnection: true,               //启动重新连接
  reconnectionAttempts: 5,          //最大重试连接次数
  reconnectionDelay: 2000,          //最初尝试新的重新连接等待时间
  reconnectionDelayMax: 10000, 
  query: {
    Authorization: `Bearer ${ token }`
  }
});

socket.on("connect", () => {
  socket.emit('newUser');

  // 監聽通知事件
  socket.on('syncNotification', () => count());
});

const count = () => notificationCount.value += 1;

const message = computed(() => {
  return `被通知了幾次 ${ notificationCount.value }`
});

// 模擬送貼文
const sendUpdateNotification = async () => {
  socket.emit('updatePost');
};

onUnmounted(() => {
  socket.close();
})

</script>