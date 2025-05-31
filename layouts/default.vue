<script setup lang="ts">
import AppSidebar from '@/components/core/AppSidebar.vue'
import {
  Breadcrumb,
  BreadcrumbItem,
  BreadcrumbLink,
  BreadcrumbList,
  BreadcrumbPage,
  BreadcrumbSeparator,
} from '@/components/ui/breadcrumb'
import { Separator } from '@/components/ui/separator'
import {
  SidebarInset,
  SidebarProvider,
  SidebarTrigger,
} from '@/components/ui/sidebar'
import { Textarea } from '@/components/ui/textarea'
</script>

<template>
  <SidebarProvider>
    <AppSidebar />
    <SidebarInset>
      <!-- Header -->
      <header
        class="flex h-20  shrink-0 items-center gap-2 transition-[width,height] ease-linear group-has-data-[collapsible=icon]/sidebar-wrapper:h-16"
      >
        <div class="flex items-center gap-2 px-4 w-full  h-full border ">
          <SidebarTrigger class="-ml-1" />
          <Separator
            orientation="vertical"
            class="mr-2 data-[orientation=vertical]:h-10"
          />
         <div class="w-full h-full flex items-center justify-end gap-2">
          <Button>
            New Message
          </Button>
          <!-- <Button>
            Save As A Template
          </Button> -->
         </div>
        </div>
      </header>
      <div class="h-[calc(100vh-5rem)] w-full">        
       <slot/>
      </div>
    </SidebarInset>
  </SidebarProvider>
</template>
