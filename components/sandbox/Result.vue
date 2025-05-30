<template>
  <div class="w-full px-30 py-4">
    <div class="grid grid-rows-2 gap-2 w-full ">
      <!-- Top Section -->
      <div class=" rounded">
        <div class="flex justify-between items-center w-full">
          <div class="h-full w-[33%] bg-muted border border-border  rounded-xl grid grid-cols-[30%_70%]">
            <div class="w-full h-full flex justify-center items-center">
              <Icon name="lucide:square-chevron-left" class="text-3xl" />
            </div>
            <div class="w-full h-full flex flex-col justify-center">
              <p class="text-xl font-bold">{{message?.compliance_score}}/10</p>
              <p class="text-sm text-muted-foreground">Compliance Score</p>
            </div>
          </div>

          <div class="h-full bg-muted w-[33%] border border-border rounded-xl grid grid-cols-[30%_70%]">
            <div class="w-full h-full flex justify-center items-center">
              <Icon name="lucide:square-chevron-left" class="text-3xl" />
            </div>
            <div class="w-full h-full flex flex-col justify-center">
              <p class="text-xl font-bold">{{message?.compliance_score}}</p>
              <p class="text-sm text-muted-foreground">Classification</p>
            </div>
          </div>

          <div class="h-full bg-muted w-[33%] border border-border rounded-xl grid grid-cols-[30%_70%]">
            <div class="w-full h-full flex justify-center items-center">
              <Icon name="lucide:square-chevron-left" class="text-3xl" />
            </div>
            <div class="w-full h-full flex flex-col justify-center">
              <p class="text-xl font-bold">{{message?.violation_category}}</p>
              <p class="text-sm text-muted-foreground">Violation Category</p>
            </div>
          </div>
        </div>

        <div class="w-full bg-muted mt-2  border border-border rounded-xl p-4 text-sm">
          <span v-html="highlightedParagraph"></span>
        </div>
           <div  class="w-full flex justify-end gap-3 mt-1 cursor-pointer">
              <Button class="cursor-pointer"  variant="outline" @click="useMessageHandler">Cancel Process</Button>
              <Button class="cursor-pointer" @click="useMessageHandler">Send For Review</Button>
            </div>
      </div>

      <div>
        <ul class="list-disc text-[#71717A] pl-5">
          <li v-for="suggestion in message?.fix_it_suggestions">{{ suggestion }}</li>
        </ul>
      </div>

      <!-- Suggestions Section -->
      <div class="h-full rounded">
        <p class="text-xl font-bold mb-3">Suggestions</p>
        <div
          class="w-full overflow-y-auto px-2 rounded-xl custom-scroll"
        >
          <div
            class="cursor-pointer"
            @click="selectSuggestion(index)"
          >
            <div
              class="w-full bg-muted border border-border rounded-xl mt-2 p-2 text-sm hover:bg-muted transition"
            >
            <!-- {{ message?.rewrite_suggestion }} -->
              Lorem, ipsum dolor sit amet consectetur adipisicing elit. Reiciendis repellat ipsum vitae
              suscipit, soluta praesentium, quo optio blanditiis voluptate sit aperiam? Dignissimos
              assumenda molestias quisquam alias illo necessitatibus fugiat cumque.
            </div>
            <div  class="w-full flex justify-end gap-3 mt-1 cursor-pointer">
              <Button class="cursor-pointer" @click="useMessageHandler">Use This Message</Button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { Icon } from '#components'
import { Button } from '@/components/ui/button'

const paragraph = `Lorem ipsum dolor sit amet consectetur adipisicing elit. Nihil cupiditate minima iste eaque
repellat ut repellendus excepturi, qui itaque impedit in provident pariatur consequatur. Minus
neque quidem incidunt harum repudiandae.`

const props = defineProps<{ message?: string; highlights?: string[] }>()
const highlights = ['Lorem', 'Minus', 'dolor', 'elit']
const highlightedParagraph = computed(() => {
  // if (!props.highlights || props.highlights.length === 0) return paragraph
  // Escape regex special chars in highlights
  const words = highlights.map(w => w.replace(/[.*+?^${}()|[\]\\]/g, '\\$&'))
  const regex = new RegExp(`\\b(${words.join('|')})\\b`, 'gi')
  return paragraph.replace(regex, '<span class="text-[#DC2626]">$1</span>')
})

const selectedSuggestion = ref<number | null>(null)

function selectSuggestion(index: number) {
  selectedSuggestion.value = selectedSuggestion.value === index ? null : index
}

function useMessageHandler() {
  if (selectedSuggestion.value !== null) {
    // Handle the message selection logic here
    console.log(`Using suggestion at index: ${selectedSuggestion.value}`)
  }
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
