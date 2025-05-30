<script setup lang="ts">
import { Separator } from '@/components/ui/separator'
</script>

<template>
    <div class="min-h-15 w-full border border-muted-foreground bg-muted rounded-md py-4 ">
<div class="flex items-center justify-between px-4">
<div class="flex items-center gap-2">
    <div class="w-10 h-10 bg-gray-200 rounded-full"></div>
    <div>
        <p class="text-sm font-semibold">John Doe</p>
        <p  class="text-xs font-light">Marketing  Specialist</p>
    </div>

</div>
<div class="flex items-center gap-2">
    <p class="text-xs font-light">11:00 AM | Today</p>
    <Icon name="lucide:ellipsis-vertical"/>
</div>
</div>
<p class="text-sm mt-4 align-center px-4">
    Lorem ipsum dolor sit amet consectetur adipisicing elit. Nulla nobis saepe necessitatibus explicabo quam nesciunt perferendis veritatis accusamus, eveniet obcaecati mollitia sunt eius fugit! Possimus, error natus. Maxime, distinctio ipsa.
</p>

    </div>
</template>