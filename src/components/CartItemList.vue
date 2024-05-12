<script setup>
import CartItems from './CartItems.vue'
import { inject } from 'vue'
const cart = inject('cart')
const removeFromCart = inject('removeFromCart')
</script>

<template>
  <div class="flex flex-col gap-5">
    <CartItems
      v-for="item in cart"
      :key="item.id"
      :title="item.name"
      :price="item.price"
      :imageUrl="item.imageUrl"
      @removeFromCart="() => removeFromCart(item)"
    />
  </div>
</template>
