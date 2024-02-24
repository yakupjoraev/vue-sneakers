<script setup>
defineProps({
  totalPrice: Number
})
const emit = defineEmits(['openDrawer'])
</script>

<template>
  <header class="flex justify-between border-b border-slate-300 px-10 py-8">
    <a class="flex items-center gap-4" href="#">
      <img class="w-10" src="/logo.png" alt="logo" />

      <div>
        <h2 class="text-xl font-bold uppercase">VUE SNEAKERS</h2>
        <p class="text-slate-400">Магазин лучших кроссовок</p>
      </div>
    </a>

    <ul class="flex items-center gap-10">
      <li
        @click="() => emit('openDrawer')"
        class="flex items-center gap-3 text-gray-500 cursor-pointer hover:text-black"
      >
        <img src="/cart.svg" alt="cart" />
        <b>{{ totalPrice }} руб.</b>
      </li>
      <li class="flex items-center gap-3 text-gray-500 cursor-pointer hover:text-black">
        <img src="/heart.svg" alt="heart" />
        <p>Закладки</p>
      </li>
      <li class="flex items-center gap-3 text-gray-500 cursor-pointer hover:text-black">
        <img src="/profile.svg" alt="profile" />
        <p>Профиль</p>
      </li>
    </ul>
  </header>
</template>