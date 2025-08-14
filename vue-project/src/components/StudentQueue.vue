<template>
  <div>
    <h2>Записаться в очередь</h2>
    <form @submit.prevent="joinQueue">
      <input v-model="name" placeholder="Ваше имя" required />
      <button type="submit">Записаться</button>
    </form>
    <div v-if="joined">
      <p>Вы записаны в очередь!</p>
    </div>
    <QueueList v-if="queue.length" :queue="queue" />
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import QueueList from './QueueList.vue';

const name = ref('');
const queue = ref<string[]>([]);
const joined = ref(false);
const queueId = ref<string | null>(null);

onMounted(() => {
  const params = new URLSearchParams(window.location.search);
  queueId.value = params.get('queue');
  if (queueId.value) {
    const saved = localStorage.getItem('queue-' + queueId.value);
    queue.value = saved ? JSON.parse(saved) : [];
  }
});

function joinQueue() {
  if (!queueId.value) return;
  if (!queue.value.includes(name.value)) {
    queue.value.push(name.value);
    localStorage.setItem('queue-' + queueId.value, JSON.stringify(queue.value));
    joined.value = true;
  }
}
</script>
