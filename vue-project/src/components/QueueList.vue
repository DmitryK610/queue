<template>
  <div>
    <h3>Очередь студентов</h3>
    <ul>
      <li v-for="(student, idx) in queue" :key="student">
        {{ idx + 1 }}. {{ student }}
        <button @click="$emit('remove', idx)">Удалить</button>
        <button @click="$emit('move', { from: idx, to: idx - 1 })" :disabled="idx === 0">↑</button>
        <button @click="$emit('move', { from: idx, to: idx + 1 })" :disabled="idx === queue.length - 1">↓</button>
      </li>
    </ul>
  </div>
</template>

<script setup lang="ts">
defineProps<{ queue: string[] }>();
defineEmits(['remove', 'move']);
</script>
