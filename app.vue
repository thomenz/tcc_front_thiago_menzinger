<script setup lang="ts">
import { z } from 'zod'
import type { FormSubmitEvent } from '#ui/types'

const schema = z.object({
  message: z.string().min(1, 'Must be at least 1 character')
})

type Schema = z.output<typeof schema>

const state = reactive({
  message: undefined,
})

const messages = ref<{who:string, msg:string, time?:string}[]>([])

async function onSubmit(event: FormSubmitEvent<Schema>) {
  loading.value = true
  messages.value.unshift({who:'user', msg:event.data.message})
  const t0 = performance.now();
  const res = await $fetch('http://127.0.0.1:8000', {
    method: 'POST',
    body: { data: event.data.message },
  })
  const t1 = performance.now();
  messages.value.unshift({who:'bot', msg:res as string, time:`${Math.round(t1 - t0)} milliseconds`})
  state.message = undefined
  loading.value = false
}

const loading = ref(false)
</script>

<template>
  <div class="flex flex-col h-[100svh] max-w-[540px] m-auto">
    <div class="flex flex-col grow justify-between p-3">
      <div class="h-14 bg-slate-900 rounded-b-lg flex flex-col justify-center p-3 w-full">
        <h1 class="text-gray-400">TCC - Thiago Menzinger - RA 20082830-5 </h1>
        <h2 class="text-xs text-gray-400">Engenharia Mecatrônica - Unicesumar</h2>
      </div>
      <div class="flex flex-col">
        <ul v-if="messages" class="gap-5 py-10 flex overflow-hidden max-h-[80svh] flex-col-reverse">
        <li v-for="msg in messages" :class="[msg.who === 'user' ?'self-end bg-green-700/35' : 'self-start bg-slate-800','p-3 rounded-lg flex flex-col space-y-2 min-w-[90px] max-w-[300px]']">
          <p>{{msg.msg}}</p>
          <div :class="[msg.time? 'justify-between':'justify-end', 'text-xs flex flex-row w-full']">
          <span v-if="msg.time" class="text-yellow-400"> {{ msg.time }}</span>
          <span>{{msg.who}}</span>
          </div>
        </li>
      </ul>
      <UForm class="flex flex-row space-x-2 items-center" :schema="schema" :state="state" @submit="onSubmit">
        <UFormGroup name="message" class="w-full">
          <UInput class="w-full" :disabled="loading" placeholder="Mensagem..." size="xl" v-model="state.message" />
        </UFormGroup>
        <UButton type="submit" icon="i-heroicons-paper-airplane" size="xl" variant="solid" color="white"
          :disabled="loading" :loading="loading"> </UButton>
      </UForm>
      </div>
    </div>
  </div>
</template>
