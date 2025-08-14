<template>
  <div>
    <button @click="createQueue">Создать очередь</button>
    <div v-if="queueId">
      <p>Ссылка для студентов:</p>
      <input :value="queueLink" readonly style="width: 100%" />
    </div>
    <QueueList v-if="queue.length" :queue="queue" @remove="removeStudent" @move="moveStudent" />
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from 'vue';
import QueueList from './QueueList.vue';

const queue = ref<string[]>([]);
const queueId = ref<string | null>(null);


// Получить очередь с сервера при наличии queueId
onMounted(async () => {
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

// Создать очередь на сервере
async function createQueue() {
  try {
    const res = await fetch('/api/queues/', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
    });
    if (res.ok) {
      const data = await res.json();
      queueId.value = data.id;
      queue.value = [];
    }
  } catch (e) {
    // обработка ошибки
  }
}


// Удалить студента на сервере
async function removeStudent(index: number) {
  if (!queueId.value) return;
  const name = queue.value[index];
  try {
    const res = await fetch('/api/queues/items/', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ action: 'remove', name, queue_id: queueId.value })
    });
    if (res.ok) {
      queue.value.splice(index, 1);
    }
  } catch (e) {
    // обработка ошибки
  }
}

// Переместить студента на сервере (например, через items с action: 'move')
async function moveStudent({ from, to }: { from: number; to: number }) {
  if (!queueId.value) return;
  try {
    const res = await fetch('/api/queues/items/', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ action: 'move', queue_id: queueId.value, from, to })
    });
    if (res.ok) {
      const item = queue.value.splice(from, 1)[0];
      queue.value.splice(to, 0, item);
    }
  } catch (e) {
    // обработка ошибки
  }
}

const queueLink = computed(() =>
  window.location.origin + '/?queue=' + queueId.value
);
</script>
