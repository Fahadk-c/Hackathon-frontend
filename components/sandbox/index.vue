<template>
  <div class="flex items-center justify-center h-full px-4">
    <div class="w-full max-w-2xl space-y-6">
      <h1 class="text-2xl font-semibold text-muted-foreground text-center">
        Test Your Message Here !
      </h1>

      <!-- Message Bubble -->
      <form
        class="w-full border border-muted-foreground bg-muted rounded-xl py-4 space-y-3"
        @submit.prevent="handleSubmit"
      >
        <!-- Textarea -->
        <div class="relative">
          <Textarea
            ref="textareaRef"
            v-model="input"
            placeholder="Type your message..."
            rows="1"
            class="w-full custom-scrollbar resize-none max-h-[150px] overflow-y-auto border-none focus:outline-none focus:ring-0 focus-visible:ring-0 rounded-md pr-3 py-1"
            @input="autoResize"
          />
        </div>

        <!-- Separator -->
        <Separator
          orientation="horizontal"
          class="w-full h-px bg-muted-foreground"
        />

        <!-- Bottom Row -->
        <div class="flex justify-between items-center pt-2 px-4">
          <!-- Dropdowns -->
          <div class="flex items-center gap-2">
            <!-- Country Select -->
            <Select v-model="selectedCountry">
              <SelectTrigger class="w-[120px] h-8 border border-muted-foreground rounded-md">
                <SelectValue placeholder="Country" />
              </SelectTrigger>
              <SelectContent class="max-h-48 overflow-y-auto">
                <SelectItem
                  v-for="country in countries"
                  :key="country"
                  :value="country"
                >
                  {{ country }}
                </SelectItem>
              </SelectContent>
            </Select>

            <!-- Industry Select -->
            <Select v-model="selectedIndustry">
              <SelectTrigger class="w-[120px] h-8 border border-muted-foreground rounded-md">
                <SelectValue placeholder="Industry" />
              </SelectTrigger>
              <SelectContent class="max-h-48 overflow-y-auto ">
                <SelectItem
                  v-for="industry in industries"
                  :key="industry"
                  :value="industry"
                >
                  {{ industry }}
                </SelectItem>
              </SelectContent>
            </Select>
          </div>

          <!-- Send Button -->
          <Button
            type="submit"
            :disabled="!input.trim()"
            class="h-8 w-8 p-0 rounded-full"
          >
            <Send class="w-4 h-4" />
          </Button>
        </div>
      </form>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, nextTick } from 'vue'
import { Send } from 'lucide-vue-next'
import { Textarea } from '@/components/ui/textarea'
import { Button } from '@/components/ui/button'
import {
  Select,
  SelectTrigger,
  SelectValue,
  SelectContent,
  SelectItem
} from '@/components/ui/select'
import { Separator } from '@/components/ui/separator'
const props = defineProps<{
  suggestedMessage?: string
}>()
const input = ref('')

import { watch } from 'vue'

watch(
  () => props.suggestedMessage,
  (newVal) => {
    if (newVal !== undefined && newVal !== null) {
      input.value = newVal
      nextTick(() => autoResize())
    }
  },
  { immediate: true }
)
// Emit message to parent
const emit = defineEmits(['message-sent'])

const textareaRef = ref<HTMLTextAreaElement | null>(null)

const selectedCountry = ref('')
const selectedIndustry = ref('')

const config = useRuntimeConfig()
const countries = ref<string[]>([])
const industries = ref<string[]>([])

const reg = await useAsyncData('countries', async () => $fetch(`${config.public.apiBase}/rules/regions`, { method: 'GET' }))
console.log(reg);
countries.value = reg.data.value || []
const ind = await useAsyncData('industries', async () => $fetch(`${config.public.apiBase}/rules/industries`, { method: 'GET' }))
industries.value = ind.data.value || []

function handleSubmit() {
  const message = input.value.trim()
  if (!message) return

  emit('message-sent', {
    sms_text: message,
    target_region: selectedCountry.value,
    industry_context: selectedIndustry.value.toLocaleUpperCase()
  })

  input.value = ''
  nextTick(() => autoResize()) // reset height
}

// Auto-grow textarea
function autoResize() {
  const el = textareaRef.value
  if (!el) return
  el.style.height = 'auto'
  el.style.height = `${el.scrollHeight}px`
}
</script>

<style scoped>
/* Custom scrollbar for dropdown content */
.custom-scrollbar::-webkit-scrollbar {
  width: 6px;
}
.custom-scrollbar::-webkit-scrollbar-track {
  background: transparent;
}
.custom-scrollbar::-webkit-scrollbar-thumb {
  background-color: rgba(100, 100, 100, 0.4);
  border-radius: 3px;
}
.custom-scrollbar {
  scrollbar-width: thin;
  scrollbar-color: rgba(100, 100, 100, 0.4) transparent;
}
</style>
