<script setup>
import Cart from './Cart.vue'
defineProps({
  items: Array
})

const emit = defineEmits(['addToFavorite', 'addToCart'])
</script>

<template>
  <div class="grid grid-cols-4 gap-10 px-8">
    <Cart
      v-for="item in items"
      :key="item.id"
      :isLiked="item.isFavorite"
      :imageUrl="item.imageUrl"
      :title="item.name"
      :price="item.price"
      :isLikedClicked="() => emit('addToFavorite', item)"
      :isAddedClicked="() => emit('addToCart', item)"
      :isAdded="item.isAdded"
    />
  </div>
</template>
