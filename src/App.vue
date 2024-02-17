<script setup>
import { onMounted, reactive, ref, watch } from 'vue'
import axios from 'axios'

import CardList from './components/CardList.vue'
import Drawer from './components/Drawer.vue'
import Header from './components/Header.vue'

const items = ref([]) // { value:[] } // когда используем ref всё становиться объектом

// filters
const filters = reactive({
  sortBy: 'title',
  searchQuery: ''
})

const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}

const onChangeSearchInput = (event) => {
  filters.searchQuery = event.target.value
}

const fetchItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy
      // searchQuery: filters.searchQuery
    }

    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }
    const { data } = await axios.get('https://bf2dfdafc6871aaa.mokky.dev/items', { params })
    items.value = data
  } catch (err) {
    console.log(err)
  }
}

onMounted(fetchItems)
watch(filters, fetchItems)

// сортировка
// const sortBy = ref('')
// const onChangeSelect = (event) => {
//   sortBy.value = event.target.value
// }

// через async & await
// onMounted(async () => {
//   try {
//     const { data } = await axios.get('https://bf2dfdafc6871aaa.mokky.dev/items')

//     items.value = data
//     console.log(items)
//   } catch (err) {
//     console.log(err)
//   }
// })

// onMounted(() => {
//   // через fetch
//   // fetch('https://bf2dfdafc6871aaa.mokky.dev/items')
//   //   .then((res) => res.json())
//   //   .then((data) => {
//   //     console.log(data)
//   //   })

//   // через axios
//   axios.get('https://bf2dfdafc6871aaa.mokky.dev/items').then((res) => res.data)
// })

// watch(filters.value, async () => {
//   try {
//     const { data } = await axios.get(
//       'https://bf2dfdafc6871aaa.mokky.dev/items?sortBy=' + filters.value.sortBy
//     )
//     items.value = data
//   } catch (err) {
//     console.log(err)
//   }
// })
</script>

<template>
  <!-- <Drawer /> -->
  <div class="w-4/5 m-auto bg-white rounded-xl shadow-xl mt-14">
    <Header />

    <div class="p-10">
      <div class="flex justify-between items-center">
        <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>

        <div class="flex gap-4">
          <select @change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none">
            <option value="name">По названию</option>
            <option value="price">По цене (дешевые)</option>
            <option value="-price">По цене (дорогие)</option>
          </select>

          <div class="relative">
            <img class="absolute left-3 top-4" src="/search.svg" alt=" search icon" />
            <input
              @change="onChangeSearchInput"
              class="border rounded-md py-2 pl-10 pr-4 outline-none focus:border-gray-400"
              type="text"
              placeholder="Поиск..."
            />
          </div>
        </div>
      </div>

      <div class="mt-10">
        <CardList :items="items" />
      </div>
    </div>
  </div>
</template>

<style scoped>
</style>
