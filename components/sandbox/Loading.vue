<template>
  <div class="w-full h-full px-30 py-4 animate-pulse">
    <div class="grid grid-rows-2 gap-2 w-full h-full">
      
      <!-- Top Section Skeleton -->
      <div class="h-full">
        <!-- Three Boxes in a Row -->
        <div class="flex justify-between items-center h-[42%] w-full gap-2">
          <div v-for="n in 3" :key="n" class="h-full w-[33%] border border-gray-200 rounded-xl p-3 space-y-2">
            <div class="grid grid-cols-[30%_70%] gap-2 h-full">
              <Skeleton class="w-full h-full rounded-md" />
              <div class="space-y-2">
                <Skeleton class="w-3/4 h-4 rounded" />
                <Skeleton class="w-full h-3 rounded" />
              </div>
            </div>
          </div>
        </div>

        <!-- Paragraph Skeleton -->
        <div class="w-full mt-2 h-[55%] border border-gray-200 rounded-xl p-4 space-y-2">
          <Skeleton class="w-full h-4 rounded" />
          <Skeleton class="w-11/12 h-4 rounded" />
          <Skeleton class="w-10/12 h-4 rounded" />
          <Skeleton class="w-1/2 h-4 rounded" />
        </div>
      </div>

      <!-- Suggestions Skeleton -->
      <div class="h-full">
        <Skeleton class="w-32 h-6 mb-3 rounded" />
        <div class="w-full h-[85%] overflow-y-auto border border-gray-200 p-4 rounded-xl space-y-4">
          <div v-for="i in 3" :key="i" class="space-y-2">
            <Skeleton class="w-full h-16 rounded-xl" />
            <div class="flex justify-end gap-3">
              <Skeleton class="w-16 h-8 rounded-md" />
              <Skeleton class="w-16 h-8 rounded-md" />
            </div>
          </div>
        </div>
      </div>

    </div>
  </div>
</template>

<script setup lang="ts">
import { Skeleton } from '@/components/ui/skeleton'
</script>
