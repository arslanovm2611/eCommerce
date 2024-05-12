<script setup>
import CartItems from './CartItems.vue'
import { inject } from 'vue'
const items = inject('items')
const removeFromFavorite = inject('removeFromFavorite')
</script>

<template>
  <div class="flex flex-col gap-5">
    <CartItems
      v-for="item in items"
      :key="item.id"
      :title="item.name"
      :price="item.price"
      :imageUrl="item.imageUrl"
      @click="removeFromFavorite(item)"
    />
  </div>
</template>
