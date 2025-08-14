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

onMounted(async () => {
  const params = new URLSearchParams(window.location.search);
  queueId.value = params.get('queue');
  if (queueId.value) {
    try {
      const res = await fetch(`/api/queues/${queueId.value}/`);
      if (res.ok) {
        const data = await res.json();
        queue.value = data.queue || [];
      }
    } catch (e) {}
  }
});

async function joinQueue() {
  if (!queueId.value) return;
  if (!queue.value.includes(name.value)) {
    try {
      const res = await fetch('/api/queues/join', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ name: name.value, queue_id: queueId.value })
      });
      if (res.ok) {
        queue.value.push(name.value);
        joined.value = true;
      }
    } catch (e) {
      // обработка ошибки
    }
  }
}
</script>
