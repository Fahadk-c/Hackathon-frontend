<template> 
    <Sandbox v-if="currentScreen === 'default'" @message-sent="handleMessage" />
    <SandboxLoading v-else-if="currentScreen === 'loading'" />
    <SandboxResult v-else-if="currentScreen === 'result'" :message="lastMessage" />
</template>

<script setup lang="ts">

// Reactive state
const currentScreen = ref<'default' | 'loading' | 'result'>('default')
const lastMessage = ref('')

// Handle message from Sandbox and toggle screens
function handleMessage(message: string) {
  lastMessage.value = message
  currentScreen.value = 'loading'

  // Simulate API delay
  setTimeout(() => {
    currentScreen.value = 'result'
  }, 5000)
}
</script>
