<script setup lang="ts">
const config = useRuntimeConfig()
const selectedTab = ref('approved');

async function changeType(type: string) {

  const response = await $fetch(`${config.public.apiBase}/review?status=${type}`, { method: 'GET'})

}
watch(selectedTab, (newVal) => {
    changeType(newVal);
});


</script>

<template>
    <div class="p-5">
        <Tabs v-model="selectedTab" class="w-[215px] mb-5">
            <TabsList class="grid w-full grid-cols-2 h-[44px]">
                <TabsTrigger value="approved">Approved</TabsTrigger>
                <TabsTrigger value="rejected">Declined</TabsTrigger>
            </TabsList>
        </Tabs>
        <div class="w-full flex flex-col gap-3">
            <ManualCheckingPastCard v-for="i in 9" :key="i" />
        </div>
    </div>
</template>
