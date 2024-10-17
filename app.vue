<script setup lang="ts">
async function callAPI() {
  loading.value = true
  if (!inputValue.value || inputValue.value === '') {
    return
  }
  const res = await $fetch('http://127.0.0.1:8000', {
    method: 'POST',
    body: { data: inputValue.value },
  })
  inputValue.value = ''
  console.log(res)
  loading.value = false
}
const inputValue = ref('')
const loading = ref(false)
</script>

<template>
  <div>
    <NuxtRouteAnnouncer />
    <div class="flex flex-col items-center h-[100svh] justify-center">
      <div class="flex flex-col space-y-7 items-center">
      <UInput :disabled="loading" placeholder="Mensagem..." size="xl" v-model="inputValue" />
      <UButton size="xl" :disabled="loading" :loading="loading" @click="callAPI()"> Make Request</UButton>
      </div>
    </div>
  </div>
</template>
