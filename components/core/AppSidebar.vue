<script setup lang="ts">
import type { SidebarProps } from '@/components/ui/sidebar'

import {
  AudioWaveform,
  BookOpen,
  Bot,
  Command,
  Frame,
  GalleryVerticalEnd,
  Map,
  PieChart,
  Settings2,
  SquareTerminal,
} from 'lucide-vue-next'
import NavMain from '@/components/core/NavMain.vue'
import NavProjects from '@/components/core/NavProjects.vue'
import NavUser from '@/components/core/NavUser.vue'
import TeamSwitcher from '@/components/core/TeamSwitcher.vue'

import {
  Sidebar,
  SidebarContent,
  SidebarFooter,
  SidebarHeader,
  SidebarRail,
} from '@/components/ui/sidebar'

const props = withDefaults(defineProps<SidebarProps>(), {
  collapsible: 'icon',
})

// This is sample data.
const data = {
  user: {
    name: 'shadcn',
    email: 'm@example.com',
    avatar: '/avatars/shadcn.jpg',
  },
  teams: [
    {
      name: 'Acme Inc',
      logo: GalleryVerticalEnd,
      plan: 'Enterprise',
    },
    {
      name: 'Acme Corp.',
      logo: AudioWaveform,
      plan: 'Startup',
    },
    {
      name: 'Evil Corp.',
      logo: Command,
      plan: 'Free',
    },
  ],
  navMain: [
    {
      title: 'Sandbox',
      url: '/app',
      icon: SquareTerminal,
      isActive: true,
      
    },
    {
      title: 'Rules',
      url: '/app/rules',
      icon: Bot,
      
    },
    {
      title: 'History',
      url: '/app/history',
      icon: BookOpen,
     
    },
    {
      title: 'Manual Checking',
      url: '/app/manual-checking',
      icon: Settings2,
      
    },
  ],
  projects: [
    {
      name: 'Design Engineering',
      url: '#',
      icon: Frame,
    },
    {
      name: 'Sales & Marketing',
      url: '#',
      icon: PieChart,
    },
    {
      name: 'Travel',
      url: '#',
      icon: Map,
    },
  ],
}
</script>

<template>
  <Sidebar v-bind="props">
    <SidebarHeader class="h-20 group-has-data-[collapsible=icon]/sidebar-wrapper:h-16">
      <TeamSwitcher :teams="data.teams" class="my-auto"/>
       <!-- <div class="border h-15 flex justify-center items-center rounded-2xl">
        Header
       </div> -->
    </SidebarHeader>
    <SidebarContent>
      <NavMain :items="data.navMain" />
      <!-- <NavProjects :projects="data.projects" /> -->
    </SidebarContent>
    <SidebarFooter>
      <!-- <NavUser :user="data.user" /> -->
       Settings
    </SidebarFooter>
    <SidebarRail />
  </Sidebar>
</template>
