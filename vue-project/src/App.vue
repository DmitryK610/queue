
<script setup lang="ts">
import { ref, onMounted } from 'vue';
import TeacherQueue from './components/TeacherQueue.vue';
import StudentQueue from './components/StudentQueue.vue';

const isStudent = ref(false);
onMounted(() => {
  const params = new URLSearchParams(window.location.search);
  isStudent.value = !!params.get('queue');
});
</script>


<template>
  <TeacherQueue v-if="!isStudent" />
  <StudentQueue v-else />
</template>

<style scoped></style>
