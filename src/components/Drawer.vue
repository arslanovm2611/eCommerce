<script setup>
import { inject } from 'vue'
import DrawerHead from './DrawerHead.vue'
import CartItemList from './CartItemList.vue'
defineProps({
  totalPrice: Number,
  vatPrice: Number
})

const closeDrawer = inject('closeDrawer')
const emit = defineEmits(['createOrder'])
</script>
<template>
  <div>
    <div
      class="fixed top-0 left-0 w-full h-full bg-black opacity-70 z-10"
      @click="closeDrawer"
    ></div>
    <div class="fixed top-0 right-0 w-96 h-full bg-white z-20 p-8 overflow-y-auto">
      <DrawerHead />
      <CartItemList />

      <div class="flexm flex-col gap-4 my-7">
        <div class="flex gap-2">
          <p>Итого:</p>
          <div class="flex-1 border-b border-dashed"></div>
          <b>{{ totalPrice }} руб.</b>
        </div>

        <div class="flex gap-2">
          <p>Налог 5%:</p>
          <div class="flex-1 border-b border-dashed"></div>
          <b>{{ vatPrice }} руб.</b>
        </div>
      </div>
      <button
        :disabled="totalPrice ? false : true"
        class="transition disabled:bg-gray-300 bg-lime-500 hover:bg-lime-600 active:bg-lime-700 w-full py-3 rounded-lg text-white"
        @click="() => emit('createOrder')"
      >
        Оформить заказ
      </button>
    </div>
  </div>
</template>
