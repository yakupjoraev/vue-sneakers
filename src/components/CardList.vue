<script setup>
import Card from './Card.vue'
</script>

<template>
  <div class="grid grid-cols-4 gap-5">
    <Card
      title="Мужские Кроссовки Nike Blazer Mid Suede"
      image-url="/sneakers/sneakers-1.jpg"
      :price="5000"
      :is-added="false"
      :is-favorite="false"
    />

    <Card
      title="Мужские Кроссовки Nike Blazer Mid Suede"
      :price="5000"
      image-url="/sneakers/sneakers-1.jpg"
      :is-added="false"
      :is-favorite="false"
    />

    <Card
      title="Мужские Кроссовки Nike Blazer Mid Suede"
      :price="5000"
      image-url="/sneakers/sneakers-1.jpg"
      :is-added="true"
      :is-favorite="true"
    />
  </div>
</template>