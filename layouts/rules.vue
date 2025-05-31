<script setup lang="ts">
import { ref } from 'vue'
import AppSidebar from '@/components/core/AppSidebar.vue'
import {
  SidebarInset,
  SidebarProvider,
  SidebarTrigger,
} from '@/components/ui/sidebar'
import { Separator } from '@/components/ui/separator'
import { Textarea } from '@/components/ui/textarea'
import { Button } from '@/components/ui/button'
import {
  Dialog,
  DialogContent,
  DialogHeader,
  DialogTitle,
  DialogDescription,
} from '@/components/ui/dialog'
import { useForm } from 'vee-validate'
import { z } from 'zod'
import { toTypedSchema } from '@vee-validate/zod'

const isDialogOpen = ref(false)

const formSchema = z.object({
  name: z.string().min(2, 'Name is required'),
  region: z.string().min(1, 'Region is required'),
  industry: z.string().min(1, 'Industry is required'),
  description: z.string().min(5, 'Description is required'),
  rule: z.string().min(5, 'Rule is required'),
})

const { handleSubmit, defineField, errors } = useForm({
  validationSchema: toTypedSchema(formSchema),
})

const [name, nameAttrs] = defineField('name')
const [region, regionAttrs] = defineField('region')
const [industry, industryAttrs] = defineField('industry')
const [description, descriptionAttrs] = defineField('description')
const [rule, ruleAttrs] = defineField('rule')

const onSubmit = handleSubmit((values) => {
  console.log('Submitted:', values)
  isDialogOpen.value = false
})
</script>

<template>
  <SidebarProvider>
    <AppSidebar />
    <SidebarInset>
      <!-- Header -->
      <header
        class="flex h-20 shrink-0 items-center gap-2 transition-[width,height] ease-linear group-has-data-[collapsible=icon]/sidebar-wrapper:h-16"
      >
        <div class="flex items-center gap-2 px-4 w-full h-full border">
          <SidebarTrigger class="-ml-1" />
          <Separator
            orientation="vertical"
            class="mr-2 data-[orientation=vertical]:h-10"
          />
          <div class="w-full h-full flex items-center justify-end gap-2">
            <Button @click="isDialogOpen = true">Add custom Rules</Button>
          </div>
        </div>
      </header>

      <!-- Dialog -->
      <Dialog :open="isDialogOpen" @update:open="val => isDialogOpen = val">
        <DialogContent>
          <DialogHeader>
            <DialogTitle>Add Custom Rule</DialogTitle>
            <DialogDescription>Fill in the details to add a new rule</DialogDescription>
          </DialogHeader>

          <form @submit.prevent="onSubmit" class="space-y-4 mt-4">
            <!-- Name -->
            <div>
              <label class="block text-sm font-medium mb-1">Name</label>
              <input
                v-model="name"
                v-bind="nameAttrs"
                class="w-full border rounded p-2"
              />
              <p class="text-red-500 text-sm">{{ errors.name }}</p>
            </div>

            <!-- Region + Industry -->
            <div class="flex gap-4">
              <div class="w-1/2">
                <label class="block text-sm font-medium mb-1">Region</label>
                <select
                  v-model="region"
                  v-bind="regionAttrs"
                  class="w-full border rounded p-2"
                >
                  <option value="">Select Region</option>
                  <option value="us">United States</option>
                  <option value="eu">Europe</option>
                  <option value="asia">Asia</option>
                </select>
                <p class="text-red-500 text-sm">{{ errors.region }}</p>
              </div>

              <div class="w-1/2">
                <label class="block text-sm font-medium mb-1">Industry</label>
                <select
                  v-model="industry"
                  v-bind="industryAttrs"
                  class="w-full border rounded p-2"
                >
                  <option value="">Select Industry</option>
                  <option value="finance">Finance</option>
                  <option value="healthcare">Healthcare</option>
                  <option value="technology">Technology</option>
                </select>
                <p class="text-red-500 text-sm">{{ errors.industry }}</p>
              </div>
            </div>

            <!-- Description -->
            <div>
              <label class="block text-sm font-medium mb-1">Description</label>
              <input
                v-model="description"
                v-bind="descriptionAttrs"
                class="w-full border rounded p-2"
              />
              <p class="text-red-500 text-sm">{{ errors.description }}</p>
            </div>

            <!-- Rule -->
            <div>
              <label class="block text-sm font-medium mb-1">Rule</label>
              <Textarea v-model="rule" v-bind="ruleAttrs" />
              <p class="text-red-500 text-sm">{{ errors.rule }}</p>
            </div>

            <!-- Buttons -->
            <div class="flex gap-4 pt-2">
              <Button
                variant="outline"
                class="w-1/2"
                type="button"
                @click="isDialogOpen = false"
              >
                Cancel
              </Button>
              <Button class="w-1/2" type="submit">Save</Button>
            </div>
          </form>
        </DialogContent>
      </Dialog>

      <!-- Main content -->
      <div class="h-[calc(100vh-5rem)] w-full">
        <slot />
      </div>
    </SidebarInset>
  </SidebarProvider>
</template>
