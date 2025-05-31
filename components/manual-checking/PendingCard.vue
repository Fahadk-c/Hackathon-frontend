<script setup lang="ts">
import { ref } from 'vue'
import { Separator } from '@/components/ui/separator'
import { Button } from '@/components/ui/button'
import {
  Dialog,
  DialogContent,
  DialogHeader,
  DialogTitle,
  DialogDescription,
} from '@/components/ui/dialog'
import { Textarea } from '@/components/ui/textarea'

const showDialog = ref(false)
const actionType = ref<'approve' | 'decline' | null>(null)
const remarks = ref('')

function openDialog(type: 'approve' | 'decline') {
  actionType.value = type
  showDialog.value = true
}

function submitRemarks() {
  console.log(`Action: ${actionType.value}, Remarks:`, remarks.value)
  // reset
  showDialog.value = false
  remarks.value = ''
  actionType.value = null
}
</script>

<template>
  <div class="min-h-15 w-full border border-muted-foreground bg-muted rounded-md py-4">
    <!-- Header -->
    <div class="flex items-center justify-between px-4">
      <div class="flex items-center gap-2">
        <div class="w-10 h-10 bg-gray-200 rounded-full"></div>
        <div>
          <p class="text-sm font-semibold">John Doe</p>
          <p class="text-xs font-light">Marketing Specialist</p>
        </div>
      </div>
      <div>
        <p class="text-xs font-light">11:00 AM | Today</p>
      </div>
    </div>

    <!-- Message -->
    <p class="text-sm mt-4 px-4">
      Lorem ipsum dolor sit amet consectetur adipisicing elit. Nulla nobis saepe necessitatibus
      explicabo quam nesciunt perferendis veritatis accusamus, eveniet obcaecati mollitia sunt eius
      fugit! Possimus, error natus. Maxime, distinctio ipsa.
    </p>

    <!-- Separator -->
    <Separator orientation="horizontal" class="h-[0.5px] w-full my-3 bg-muted-foreground" />

    <!-- Actions -->
    <div class="w-full flex justify-end items-center px-4 gap-2">
      <Button variant="ghost" class="border border-white" @click="openDialog('decline')">
        Decline
      </Button>
      <Button @click="openDialog('approve')">Approve</Button>
    </div>
  </div>

  <!-- Dialog -->
  <Dialog :open="showDialog" @update:open="showDialog = $event">
    <DialogContent>
      <DialogHeader>
        <DialogTitle>{{ actionType === 'approve' ? 'Approve Request' : 'Decline Request' }}</DialogTitle>
        <DialogDescription>Please provide remarks for this action.</DialogDescription>
      </DialogHeader>

      <div class="mt-4 space-y-2">
        <label class="text-sm font-medium">Remarks</label>
        <Textarea v-model="remarks" placeholder="Enter your remarks..." />
      </div>

      <div class="flex justify-end gap-2 pt-4">
        <Button variant="outline" @click="showDialog = false">Cancel</Button>
        <Button @click="submitRemarks">Done</Button>
      </div>
    </DialogContent>
  </Dialog>
</template>
