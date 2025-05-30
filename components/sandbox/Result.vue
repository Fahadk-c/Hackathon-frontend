<template>
  <div class="w-full h-full px-30 py-4">
    <div class="grid grid-rows-2 gap-2 w-full h-full">
      <!-- Top Section -->
      <div class="h-full rounded">
        <div class="flex justify-between items-center h-[42%] w-full">
          <div class="h-full w-[33%] bg-muted border border-border  rounded-xl grid grid-cols-[30%_70%]">
            <div class="w-full h-full flex justify-center items-center">
              <Icon name="lucide:square-chevron-left" class="text-3xl" />
            </div>
            <div class="w-full h-full flex flex-col justify-center">
              <p class="text-xl font-bold">Heading</p>
              <p class="text-sm text-muted-foreground">This is the description for the heading</p>
            </div>
          </div>

          <div class="h-full bg-muted w-[33%] border border-border rounded-xl grid grid-cols-[30%_70%]">
            <div class="w-full h-full flex justify-center items-center">
              <Icon name="lucide:square-chevron-left" class="text-3xl" />
            </div>
            <div class="w-full h-full flex flex-col justify-center">
              <p class="text-xl font-bold">Heading</p>
              <p class="text-sm text-muted-foreground">This is the description for the heading</p>
            </div>
          </div>

          <div class="h-full bg-muted w-[33%] border border-border rounded-xl grid grid-cols-[30%_70%]">
            <div class="w-full h-full flex justify-center items-center">
              <Icon name="lucide:square-chevron-left" class="text-3xl" />
            </div>
            <div class="w-full h-full flex flex-col justify-center">
              <p class="text-xl font-bold">Heading</p>
              <p class="text-sm text-muted-foreground">This is the description for the heading</p>
            </div>
          </div>
        </div>

        <div class="w-full bg-muted mt-2 h-[55%] border border-border rounded-xl p-4 text-sm">
          Lorem ipsum dolor sit amet consectetur adipisicing elit. Nihil cupiditate minima iste eaque
          repellat ut repellendus excepturi, qui itaque impedit in provident pariatur consequatur. Minus
          neque quidem incidunt harum repudiandae.
        </div>
      </div>

      <!-- Suggestions Section -->
      <div class="h-full rounded">
        <p class="text-xl font-bold mb-3">Suggestions</p>
        <div
          class="w-full h-[85%] overflow-y-auto px-2 rounded-xl custom-scroll"
        >
          <div
            v-for="(suggestion, index) in 4"
            :key="index"
            class="cursor-pointer"
            @click="selectSuggestion(index)"
          >
            <div
              class="w-full bg-muted border border-border rounded-xl mt-2 p-2 text-sm hover:bg-muted transition"
            >
              Lorem, ipsum dolor sit amet consectetur adipisicing elit. Reiciendis repellat ipsum vitae
              suscipit, soluta praesentium, quo optio blanditiis voluptate sit aperiam? Dignissimos
              assumenda molestias quisquam alias illo necessitatibus fugiat cumque.
            </div>
            <div v-if="selectedSuggestion === index" class="w-full flex justify-end gap-3 mt-1">
              <Button>Edit</Button>
              <Button>Use</Button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import { Icon } from '#components'
import { Button } from '@/components/ui/button'

const selectedSuggestion = ref<number | null>(null)

function selectSuggestion(index: number) {
  selectedSuggestion.value = selectedSuggestion.value === index ? null : index
}
</script>

<style scoped>
.custom-scroll::-webkit-scrollbar {
  width: 8px;
}
.custom-scroll::-webkit-scrollbar-track {
  background: transparent;
}
.custom-scroll::-webkit-scrollbar-thumb {
  background-color: rgba(239, 243, 248, 0.4); /* Slate-500 */
  border-radius: 8px;
  border: 2px solid transparent;
  background-clip: padding-box;
}
.custom-scroll:hover::-webkit-scrollbar-thumb {
  background-color: rgba(255, 255, 255, 0.692); /* Darker on hover */
}
</style>
