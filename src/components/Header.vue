<script setup>
defineProps({
  showDrawer: Function,
  totalPrice: Number
})

const emit = defineEmits(['showDrawerFav'])
</script>

<template>
  <header class="flex justify-between border-b border-slate-3000 px-8 py-10">
    <div class="flex items-center gap-4">
      <img src="/logo.png" alt="Logo" class="w-10" />
      <div>
        <h2 class="text-xl font-bold uppercase">Vue sneakers</h2>
        <p class="text-slate-400">Магазин лучших кроссовок</p>
      </div>
    </div>

    <ul class="flex gap-10 pt-6/2 items-center">
      <li
        class="flex items-center gap-3 text-gray-500 hover:text-black cursor-pointer"
        @click="showDrawer"
      >
        <img src="/cart.svg" alt="" />
        <b>{{ totalPrice }} руб.</b>
      </li>

      <li
        @click="() => emit('showDrawerFav')"
        class="flex items-center gap-3 text-gray-500 hover:text-black cursor-pointer"
      >
        <img src="/heart.svg" alt="" />
        <span>Закладки</span>
      </li>

      <li class="flex items-center gap-3 text-gray-500 hover:text-black cursor-pointer">
        <img src="/profile.svg" alt="" />
        <span>Профиль</span>
      </li>
    </ul>
  </header>
</template>
