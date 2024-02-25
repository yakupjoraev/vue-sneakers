<script setup>
import { computed, provide, ref, watch } from 'vue'

import Drawer from './components/Drawer.vue'
import Header from './components/Header.vue'

/* КОРЗИНА */
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

const addToCart = (item) => {
  cart.value.push(item)
  item.isAdded = true
}

const removeFromCart = (item) => {
  cart.value.splice(cart.value.indexOf(item), 1)
  item.isAdded = false
}

watch(
  cart,
  () => {
    localStorage.setItem('item', JSON.stringify(cart.value))
  },
  { deep: true }
)
provide('cart', {
  cart,
  closeDrawer,
  openDrawer,
  addToCart,
  removeFromCart
})

/* КОРЗИНА */

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
  <Drawer v-if="drawerOpen" :total-price="totalPrice" :vat-price="vatPrice" />

  <div class="w-4/5 m-auto bg-white rounded-xl shadow-xl mt-14">
    <Header :total-price="totalPrice" @open-drawer="openDrawer" />

    <div class="p-10">
      <router-view></router-view>
    </div>
  </div>
</template>

<style scoped>
</style>
