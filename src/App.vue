<script setup>
import { computed, onMounted, provide, reactive, ref, watch } from 'vue'
import axios from 'axios'

import CardList from './components/CardList.vue'
import Drawer from './components/Drawer.vue'
import Header from './components/Header.vue'

const items = ref([]) // { value:[] } // когда используем ref всё становиться объектом
const cart = ref([])

const drawerOpen = ref(false)

const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0))

const vatPrice = computed(() => Math.round((totalPrice.value * 5) / 100))

const closeDrawer = () => {
  drawerOpen.value = false
}

const openDrawer = () => {
  drawerOpen.value = true
}

// filters
const filters = reactive({
  sortBy: 'title',
  searchQuery: ''
})

const addToCart = (item) => {
  cart.value.push(item)
  item.isAdded = true
}

const removeFromCart = (item) => {
  cart.value.splice(cart.value.indexOf(item), 1)
  item.isAdded = false
}

const createOrder = async () => {
  try {
    const { data } = await axios.post(`https://bf2dfdafc6871aaa.mokky.dev/orders`, {
      items: cart.value,
      totalPrice: totalPrice.value
    })
    cart.value = []

    return data
  } catch (error) {
    console.log(error)
  }
}

const onnClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeFromCart(item)
  }

  console.log(cart)
}

const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}

const onChangeSearchInput = (event) => {
  filters.searchQuery = event.target.value
}

const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get('https://bf2dfdafc6871aaa.mokky.dev/favorites')
    items.value = items.value.map((item) => {
      const favorite = favorites.find((favorite) => favorite.parentId === item.id)

      if (!favorite) {
        return item
      }

      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id
      }
    })
  } catch (err) {
    console.log(err)
  }
}

const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      const obj = {
        parentId: item.id
      }
      const { data } = await axios.post(`https://bf2dfdafc6871aaa.mokky.dev/favorites`, obj)

      item.isFavorite = true
      item.favoriteId = data.id

      console.log(data)
    } else {
      await axios.delete(`https://bf2dfdafc6871aaa.mokky.dev/favorites/${item.favoriteId}`)
      item.isFavorite = false
      item.favoriteId = null
    }
  } catch (err) {
    console.log(err)
  }
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

    items.value = data.map((obj) => ({
      ...obj,
      isFavorite: false,
      favoriteId: null,
      isAdded: false
    }))
  } catch (err) {
    console.log(err)
  }
}

onMounted(async () => {
  await fetchItems()
  await fetchFavorites()
})
watch(filters, fetchItems)

provide('cart', {
  cart,
  closeDrawer,
  openDrawer,
  addToCart,
  removeFromCart
})

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
  <Drawer
    v-if="drawerOpen"
    :total-price="totalPrice"
    :vat-price="vatPrice"
    @create-order="createOrder"
  />

  <div class="w-4/5 m-auto bg-white rounded-xl shadow-xl mt-14">
    <Header :total-price="totalPrice" @open-drawer="openDrawer" />

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
        <CardList :items="items" @add-to-favorite="addToFavorite" @add-to-cart="onnClickAddPlus" />
      </div>
    </div>
  </div>
</template>

<style scoped>
</style>
