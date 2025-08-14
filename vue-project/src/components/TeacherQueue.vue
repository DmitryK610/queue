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
import { ref, computed } from 'vue';
import QueueList from './QueueList.vue';

const queue = ref<string[]>([]);
const queueId = ref<string | null>(null);

const createQueue = () => {
  queueId.value = Math.random().toString(36).substring(2, 10); 
  queue.value = [];
  localStorage.setItem('queue-' + queueId.value, JSON.stringify(queue.value));
};

const queueLink = computed(() =>
  window.location.origin + '/?queue=' + queueId.value
);

function removeStudent(index: number) {
  queue.value.splice(index, 1);
  if (queueId.value) {
    localStorage.setItem('queue-' + queueId.value, JSON.stringify(queue.value));
  }
}

function moveStudent({ from, to }: { from: number; to: number }) {
  const item = queue.value.splice(from, 1)[0];
  queue.value.splice(to, 0, item);
  if (queueId.value) {
    localStorage.setItem('queue-' + queueId.value, JSON.stringify(queue.value));
  }
}
</script>
