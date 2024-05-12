<script setup>
defineProps({
  imageUrl: String,
  title: String,
  price: String,
  isLikedClicked: Function,
  isAddedClicked: Function,
  isLiked: Boolean,
  isAdded: Boolean
})
</script>

<template>
  <div>
    <div
      class="relative mt-10 border rounded-3xl border-slate-100 p-8 cursor-pointer transition hover:-translate-y-2 hover:shadow-xl"
    >
      <img
        :src="isLiked ? '/like-2.svg' : '/like-1.svg'"
        @click="isLikedClicked"
        alt=""
        class="absolute top-8 left-8"
      />
      <img :src="imageUrl" alt="" />
      <p class="mt-2">{{ title }}</p>
      <div class="flex justify-between mt-5">
        <div class="">
          <p class="text-gray-400 text-xs">ЦЕНА:</p>
          <b class="text-sm">{{ price }}руб.</b>
        </div>
        <img :src="isAdded ? '/checked.svg' : '/plus.svg'" @click="isAddedClicked" alt="" />
      </div>
    </div>
  </div>
</template>
