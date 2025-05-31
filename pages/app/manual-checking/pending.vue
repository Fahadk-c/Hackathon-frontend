<script setup lang="ts">
const config = useRuntimeConfig()
const rules = ref([])
const response = await useAsyncData('review', async () => $fetch(`${config.public.apiBase}/review`, { method: 'GET' }))

rules.value = response.data.value || []

</script>

<template>
    <div class="w-full p-5 flex flex-col gap-3">
    <ManualCheckingPendingCard v-for="rule in rules" :key="rule.id" :rule="rule"/>
    </div>
</template>