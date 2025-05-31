<template>
  <Sandbox v-if="currentScreen === 'default'" @message-sent="handleMessage" :suggestedMessage="suggestedMessage"/>
  <SandboxLoading v-else-if="currentScreen === 'loading'" />
  <SandboxResult v-else-if="currentScreen === 'result'" :message="lastMessage" :initialData="initialData" @use-message="handleUseMessage"/>
</template>

<script setup lang="ts">

const config = useRuntimeConfig()

// Reactive state
const currentScreen = ref<'default' | 'loading' | 'result'>('default')
const lastMessage = ref(null)
const initialData = ref(null)
const suggestedMessage = ref<string | null>(null)
// Handle message from Sandbox and toggle screens
async function handleMessage(data: string) {
  currentScreen.value = 'loading'
  initialData.value = data
  const response = await $fetch(`${config.public.apiBase}/compliance/check-compliance`, { method: 'POST', body: { ...data, sender_id: 'LENDER1', message_id: 'h' } })

  lastMessage.value = response 
    currentScreen.value = 'result'
}

const handleUseMessage = (message: string) => {
  currentScreen.value = 'default'
  suggestedMessage.value = message
}
</script>
