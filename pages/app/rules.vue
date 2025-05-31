<script setup lang="ts">
const config = useRuntimeConfig()

import {
  createColumnHelper,
  FlexRender,
  getCoreRowModel,
  getSortedRowModel,
  useVueTable,
} from '@tanstack/vue-table'
import { ChevronsUpDown, Eye, MoreVertical } from 'lucide-vue-next'
import { h, ref } from 'vue'
import { Button } from '@/components/ui/button'
import { Separator } from '@/components/ui/separator'
import {
  Table,
  TableBody,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from '@/components/ui/table'

import {
  Dialog,
  DialogContent,
  DialogHeader,
  DialogTitle,
  DialogTrigger,
  DialogDescription,
} from '@/components/ui/dialog'

interface Rule {
  id: string
  name: string
  description: string
  region: string
  industry: string
  classification: 'default' | 'custom'
  status: boolean
}

const selectedRule = ref<Rule | null>(null)


const columnHelper = createColumnHelper<Rule>()

const columns = [
  columnHelper.display({
    id: 'serial',
    header: '#',
    cell: ({ row }) => h('div', { class: 'text-center' }, row.index + 1),
  }),
  columnHelper.accessor('name', {
    header: ({ column }) =>
      h(Button, {
        variant: 'ghost',
        class: 'text-center w-full',
        onClick: () => column.toggleSorting(column.getIsSorted() === 'asc'),
      }, () => ['Rule', h(ChevronsUpDown, { class: 'ml-2 h-4 w-4' })]),
    cell: ({ row }) => h('div', { class: 'text-center' }, row.getValue('name')),
  }),
  columnHelper.accessor('description', {
    header: 'Description',
    cell: ({ row }) =>
      h('div', {
        class: 'text-center max-w-[300px] mx-auto truncate',
      }, row.getValue('description')),
  }),
  columnHelper.accessor('applicable_regions', {
    header: ({ column }) =>
      h(Button, {
        variant: 'ghost',
        class: 'text-center w-full',
        onClick: () => column.toggleSorting(column.getIsSorted() === 'asc'),
      }, () => ['Region', h(ChevronsUpDown, { class: 'ml-2 h-4 w-4' })]),
    cell: ({ row }) => {
      const regions = row.getValue('applicable_regions')
      // Handle array or fallback to string
      if (Array.isArray(regions)) {
        return h('div', { class: 'text-center' }, regions.join(', '))
      }
      return h('div', { class: 'text-center' }, regions || '')
    },
  }),
  columnHelper.accessor('applicable_industries', {
    header: ({ column }) =>
      h(Button, {
        variant: 'ghost',
        class: 'text-center w-full',
        onClick: () => column.toggleSorting(column.getIsSorted() === 'asc'),
      }, () => ['Industry', h(ChevronsUpDown, { class: 'ml-2 h-4 w-4' })]),
    cell: ({ row }) => {
      const industries = row.getValue('applicable_industries')
      // Handle array or fallback to string
      if (Array.isArray(industries)) {
        return h('div', { class: 'text-center' }, industries.join(', '))
      }
      return h('div', { class: 'text-center' }, industries || '')
    },
  }),
  columnHelper.accessor('is_active', {
    header: 'Status',
    cell: ({ row }) => {
      const id = `toggle-${row.original.id}`
      const isChecked = row.getValue('is_active')
      return h('label', {
        for: id,
        class: `relative block h-5 w-10 mx-auto rounded-full transition-colors 
                bg-gray-300 has-checked:bg-green-500 dark:bg-gray-600 
                dark:has-checked:bg-green-600`,
      }, [
        h('input', {
          id,
          type: 'checkbox',
          checked: isChecked,
          class: 'peer sr-only',
          onChange: (e: Event) => {
            const target = e.target as HTMLInputElement
            console.log(`Status changed for ${row.original.name}:`, target.checked)
          }
        }),
        h('span', {
          class: `absolute inset-y-0 start-0 m-[2px] size-4 rounded-full bg-white 
                  transition-[inset-inline-start] peer-checked:start-5 dark:bg-gray-900`
        }),
      ])
    },
  }),
  columnHelper.accessor('rule_type', {
    header: 'Status',
    cell: ({ row }) =>
      h('div', {
        class: 'text-center max-w-[300px] mx-auto truncate',
      }, row.getValue('rule_type')),
  }),
  columnHelper.display({
    id: 'actions',
    header: 'Actions',
    cell: ({ row }) =>
      h('div', { class: 'flex justify-center gap-2' }, [
        h(DialogTrigger, {
          asChild: true,
          onClick: () => {
            selectedRule.value = row.original
          }
        }, h(Button, {
          size: 'icon',
          class: 'h-8 w-8 border-white border bg-white',
        }, h(Eye, { class: 'h-4 w-4' }))),
        h(Button, {
          variant: 'ghost',
          size: 'icon',
          class: 'h-8 w-8',
          onClick: () => console.log('More options', row.original.id),
        }, h(MoreVertical, { class: 'h-4 w-4' })),
      ]),
  }),
]
const rules = ref<Rule[]>([])

const table = useVueTable({
  data : rules,
  columns,
  getCoreRowModel: getCoreRowModel(),
  getSortedRowModel: getSortedRowModel(),
})

const loading = ref(false)

definePageMeta({
  layout: 'rules'
})

const response = await useAsyncData('rules', async () => $fetch(`${config.public.apiBase}/rules`, { method: 'GET' }))

rules.value = response.data.value || []


</script>

<template>
  <Dialog>
    <div class="rounded-md border m-5">
      <Table>
        <TableHeader>
          <TableRow
            v-for="headerGroup in table.getHeaderGroups()"
            :key="headerGroup.id"
            class="bg-muted"
          >
            <TableHead
              v-for="header in headerGroup.headers"
              :key="header.id"
              class="text-center"
            >
              <FlexRender
                v-if="!header.isPlaceholder"
                :render="header.column.columnDef.header"
                :props="header.getContext()"
              />
            </TableHead>
          </TableRow>
        </TableHeader>
        <TableBody>
          <TableRow v-if="loading">
            <TableCell :colspan="columns.length" class="h-24 text-center">
              Loading...
            </TableCell>
          </TableRow>
          <TableRow v-else-if="table.getRowModel().rows?.length === 0">
            <TableCell :colspan="columns.length" class="h-24 text-center">
              No rules found.
            </TableCell>
          </TableRow>
          <TableRow
            v-else
            v-for="row in table.getRowModel().rows"
            :key="row.id"
            class="bg-muted/70 h-18"
          >
            <TableCell
              v-for="cell in row.getVisibleCells()"
              :key="cell.id"
              class="text-center"
            >
              <FlexRender
                :render="cell.column.columnDef.cell"
                :props="cell.getContext()"
              />
            </TableCell>
          </TableRow>
        </TableBody>
      </Table>
    </div>

    <!-- Dialog content to show selected rule details -->
    <DialogContent>
      <DialogHeader>
        <DialogTitle v-if="selectedRule">{{ selectedRule.name }}</DialogTitle>
        <Separator
          orientation="horizontal"
          class="w-full h-px bg-muted-foreground"
        />
        <DialogDescription v-if="selectedRule">
          <!-- <div class="space-y-2 mt-4 text-left">
            <div><strong>Name:</strong> {{ selectedRule.name }}</div>
            <div><strong>Description:</strong> {{ selectedRule.description }}</div>
            <div><strong>Region:</strong> {{ selectedRule.region }}</div>
            <div><strong>Industry:</strong> {{ selectedRule.industry }}</div>
            <div><strong>Classification:</strong> {{ selectedRule.classification }}</div>
            <div><strong>Status:</strong> {{ selectedRule.status ? 'Enabled' : 'Disabled' }}</div>
          </div> -->
          <div class="flex justify-between items-center" >
<div class="w-[50%]">
    <p>Region</p>
    <p class="text-sm text-white">{{ selectedRule.region }}</p>
</div>
<div class="w-[50%]">
    <p>Industry</p>
    <p class="text-sm text-white">{{ selectedRule.industry }}</p>
</div>
          </div>
          <div class="w-full pt-2">
    <p>Description</p>
    <p class="text-sm text-white">{{ selectedRule.description }}</p>
</div>
<div class="w-full pt-2 max-h-[200px] overflow-y-auto">
    <p>Description</p>
    <p class="text-sm text-white">Lorem, ipsum dolor sit amet consectetur adipisicing elit. Aspernatur corporis dolores, impedit ex soluta voluptatem doloribus quasi minima fugit earum possimus eaque rem, minus aut ut. Itaque magnam ex assumenda!Lorem, ipsum dolor sit amet consectetur adipisicing elit. Aspernatur corporis dolores, impedit ex soluta voluptatem doloribus quasi minima fugit earum possimus eaque rem, minus aut ut. Itaque magnam ex assumenda!Lorem, ipsum dolor sit amet consectetur adipisicing elit. Aspernatur corporis dolores, impedit ex soluta voluptatem doloribus quasi minima fugit earum possimus eaque rem, minus aut ut. Itaque magnam ex assumenda!  </p>
</div>
        </DialogDescription>
      </DialogHeader>
    </DialogContent>
  </Dialog>
</template>
