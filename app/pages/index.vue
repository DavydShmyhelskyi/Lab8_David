<script setup lang="ts">
import { h, resolveComponent, computed, ref } from 'vue'
import { getPaginationRowModel } from '@tanstack/vue-table'
import type { TableColumn } from '@nuxt/ui'
import { UInput } from '#components'

const UButton = resolveComponent('UButton')

const table = useTemplateRef('table')

type Product = {
  id: number
  title: string
  description: string
  price: number
  rating: number
  brand: string
  category: string
  thumbnail: string
}

const search = ref('')
const { data: apiData } = await useFetch<{ products: Product[] }>('https://dummyjson.com/products')
const originalData = ref<Product[]>(apiData.value?.products || [])

const totalRows = originalData.value.length

const filteredData = computed(() => {
  const q = (search.value || '').toLowerCase().trim()
  if (!q) return originalData.value

  return originalData.value.filter(product => {
    const fieldsToSearch = [
      product.title,
      product.description,
      product.price.toString(),
      product.rating.toString(),
      product.brand,
      product.category
    ]
    return fieldsToSearch.some(field => field?.toLowerCase().includes(q))
  })
})

const columns: TableColumn<Product>[] = [
  {
    accessorKey: 'thumbnail',
    header: () => h('div', { class: 'px-2 py-2 font-medium' }, 'Фото'),
    cell: ({ row }) =>
        h('img', {
          src: row.getValue('thumbnail') || '/default-image.png',
          alt: 'Thumbnail',
          width: 70,
          height: 70,
          class: 'object-cover rounded-lg shadow-sm'
        })
  },
  {
    accessorKey: 'title',
    header: () => h('div', { class: 'px-2 py-2 font-medium' }, 'Назва')
  },
  {
    accessorKey: 'description',
    header: () => h('div', { class: 'px-2 py-2 font-medium' }, 'Опис'),
    cell: ({ row }) => {
      const description = row.getValue('description') as string
      return h(
          'p',
          {
            class: 'whitespace-normal break-words text-gray-700',
            title: description
          },
          description || 'Опис відсутній'
      )
    }
  },
  {
    accessorKey: 'price',
    header: ({ column }) => {
      const isSorted = column.getIsSorted()
      return h(UButton, {
        color: 'neutral',
        variant: 'ghost',
        label: 'Ціна',
        icon: isSorted
            ? isSorted === 'asc'
                ? 'i-lucide-arrow-up-narrow-wide'
                : 'i-lucide-arrow-down-wide-narrow'
            : 'i-lucide-arrow-up-down',
        class: '-mx-2.5',
        onClick: () => column.toggleSorting(column.getIsSorted() === 'asc')
      })
    },
    cell: ({ row }) => {
      const price = Number(row.getValue('price'))
      return h('div', { class: 'text-right font-medium' }, `${price} $`)
    }
  },
  {
    accessorKey: 'rating',
    header: ({ column }) => {
      const isSorted = column.getIsSorted()
      return h(UButton, {
        color: 'neutral',
        variant: 'ghost',
        label: 'Оцінка',
        icon: isSorted
            ? isSorted === 'asc'
                ? 'i-lucide-arrow-up-narrow-wide'
                : 'i-lucide-arrow-down-wide-narrow'
            : 'i-lucide-arrow-up-down',
        class: '-mx-2.5',
        onClick: () => column.toggleSorting(column.getIsSorted() === 'asc')
      })
    },
    cell: ({ row }) => {
      const rating = Number(row.getValue('rating'))
      const color = rating < 4.5 ? 'text-red-600' : 'text-green-600'
      return h('span', { class: `font-bold ${color}` }, rating.toFixed(1))
    }
  },
  {
    accessorKey: 'brand',
    header: () => h('div', { class: 'px-2 py-2 font-medium' }, 'Бренд'),
    cell: ({ row }) => row.getValue('brand') || 'Бренд відсутній'
  },
  {
    accessorKey: 'category',
    header: () => h('div', { class: 'px-2 py-2 font-medium' }, 'Категорія')
  }
]


const pagination = ref({
  pageIndex: 0,
  pageSize: 5
})

const sorting = ref([
  {
    id: 'price',
    desc: false
  }
])
</script>

<template>
  <div class="container mx-auto p-6 space-y-6">
    <h1 class="text-2xl font-bold text-center text-white-800">Список продуктів</h1>

    <div class="flex justify-center">
      <UInput
          v-model="search"
          icon="i-lucide-search"
          placeholder="Пошук за назвою, описом, ціною, оцінкою, брендом чи категорією..."
          size="lg"
          class="w-1/2"
      />
    </div>

    <div class="relative bg-black shadow-md rounded-lg overflow-hidden">
      <UTable
          ref="table"
          v-model:pagination="pagination"
          v-model:sorting="sorting"
          :data="filteredData"
          :columns="columns"
          :pagination-options="{ getPaginationRowModel: getPaginationRowModel() }"
          class="w-full border-collapse border border-gray-300"
      />
    </div>

    <div class="flex justify-center pt-4">
      <UPagination
          :default-page="(table?.tableApi?.getState().pagination.pageIndex || 0) + 1"
          :items-per-page="table?.tableApi?.getState().pagination.pageSize"
          :total="table?.tableApi?.getFilteredRowModel().rows.length || totalRows"
          @update:page="(p) => table?.tableApi?.setPageIndex(p - 1)"
      />
    </div>
  </div>
</template>
