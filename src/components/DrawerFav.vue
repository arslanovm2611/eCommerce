<script setup>
import { inject } from 'vue'
import DrawerHeadFav from './DrawerHeadFav.vue'
import CartItemListFav from './CartItemListFav.vue'

const closeDrawer = inject('closeDrawer')
</script>
<template>
  <div>
    <div
      class="fixed top-0 left-0 w-full h-full bg-black opacity-70 z-10"
      @click="closeDrawer"
    ></div>
    <div class="fixed top-0 right-0 w-96 h-full bg-white z-20 p-8 overflow-y-auto">
      <DrawerHeadFav />
      <CartItemListFav />
    </div>
  </div>
</template>
