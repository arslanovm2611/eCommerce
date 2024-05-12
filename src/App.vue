<script setup>
import Header from './components/Header.vue'
import CartList from './components/CartList.vue'
import Drawer from './components/Drawer.vue'
import DrawerFav from './components/DrawerFav.vue'
import { ref, onMounted, watch, reactive, provide, computed } from 'vue'
import axios from 'axios'

const items = ref([])
const favItems = ref([])
const drawerOpen = ref(false)
const cart = ref([])
const drawerFavOpen = ref(false)
const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + Number(item.price), 0))
const vatPrice = computed(() => (totalPrice.value * 5) / 100)
const filters = reactive({
  sortBy: '',
  searchQuery: ''
})

const createOrder = async () => {
  try {
    const { data } = await axios.post('https://be35d7bfbe41519e.mokky.dev/orders', {
      items: cart,
      totalPrice: totalPrice.value
    })

    cart.value = []

    return data
  } catch (err) {
    console.error(err)
  }
}
const onClickChangeSort = (e) => {
  filters.sortBy = e.target.value
}
const onClickSearch = (e) => {
  filters.searchQuery = e.target.value
}
const fetchFavorites = async () => {
  try {
    const { data } = await axios.get('https://be35d7bfbe41519e.mokky.dev/favorites')
    items.value = items.value.map((item) => {
      const favorite = data.find((favourite) => favourite.parentId === item.id)
      if (!favorite) {
        return item
      }
      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id
      }
    })
  } catch (err) {
    console.error(err)
  }
}
const fetchItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy
    }

    if (filters.searchQuery) {
      params.name = `*${filters.searchQuery}*`
    }
    const { data } = await axios.get(`https://be35d7bfbe41519e.mokky.dev/items/`, { params })
    items.value = data

    items.value = data.map((obj) => ({
      ...obj,
      isFavorite: false,
      favoriteId: null,
      isAdded: false
    }))
  } catch (err) {
    console.error(err)
  }
}
const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      const obj = {
        parentId: item.id
      }

      item.isFavorite = true
      const { data } = await axios.post(`https://be35d7bfbe41519e.mokky.dev/favorites`, obj)
      item.favoriteId = data.id
    } else {
      item.isFavorite = false
      await axios.delete(`https://be35d7bfbe41519e.mokky.dev/favorites/${item.favoriteId}`)
      item.favoriteId = null
    }
  } catch (err) {
    console.error(err)
  }
}
const addToCart = (item) => {
  item.isAdded = true
  // await axios.post(`https://be35d7bfbe41519e.mokky.dev/orders`, item)
  cart.value.push(item)
}
const removeFromCart = (item) => {
  item.isAdded = false
  cart.value.splice(cart.value.indexOf(item), 1)
  // await axios.delete(`https://be35d7bfbe41519e.mokky.dev/orders/${item.id}`)
}
const onClickAddToCart = async (item) => {
  try {
    if (!item.isAdded) {
      addToCart(item)
    } else {
      removeFromCart(item)
    }
  } catch (err) {
    console.error(err)
  }
}

onMounted(async () => {
  const localCart = localStorage.getItem('cart')
  cart.value = localCart ? JSON.parse(localCart) : []
  await fetchItems()
  await fetchFavorites()

  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
  }))
})

const closeDrawer = () => {
  drawerOpen.value = false
  drawerFavOpen.value = false
}

const showDrawer = () => {
  drawerOpen.value = true
}

const hideDrawer = () => {
  drawerOpen.value = false
}

const showDraweFav = () => {
  drawerFavOpen.value = true
}
const hideDrawerFav = () => {
  drawerFavOpen.value = false
}
const removeFromFavorite = async (item) => {
  console.log('completed')
  item.isFavorite = false
  await axios.delete(`https://be35d7bfbe41519e.mokky.dev/favorites/${item.favoriteId}`)
}

favItems.value = items.value.filter((i) => i.isFavorite)
provide('removeFromFavorite', removeFromFavorite)
provide('removeFromCart', removeFromCart)
provide('items', favItems)
provide('cart', cart)
provide('addToCart', addToCart)
provide('hideDrawerFav', hideDrawerFav)
provide('hideDrawer', hideDrawer)
provide('closeDrawer', closeDrawer)

watch(
  items,
  () => {
    favItems.value = items.value.filter((i) => i.isFavorite)
  },
  {
    deep: true
  }
)
watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  }))
})
watch(
  cart,
  () => {
    localStorage.setItem('cart', JSON.stringify(cart.value))
  },
  { deep: true }
)
watch(filters, fetchItems)
</script>

<template>
  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
    <Drawer
      v-if="drawerOpen"
      :totalPrice="totalPrice"
      :vatPrice="vatPrice"
      @create-order="createOrder"
    />
    <DrawerFav v-if="drawerFavOpen" />
    <Header :showDrawer="showDrawer" @showDrawerFav="showDraweFav" :totalPrice="totalPrice" />

    <div class="p-10">
      <div class="flex justify-between items-center">
        <h2 class="text-3xl font-bold mt-8 mb-8">Все кроссовки</h2>
        <div class="flex gap-4">
          <select @change="onClickChangeSort" class="py-2 px-3 border rounded-md outline-none">
            <option value="name">По название</option>
            <option value="price">По цене (дешевые)</option>
            <option value="-price">По цене (дгорогие)</option>
          </select>
          <div class="relative">
            <img src="/search.svg" class="absolute top-3 left-4" />
            <input
              @input="onClickSearch"
              type="text"
              class="border rounded-md pl-11 py-2 pr-4 outline-none focus:border-gray-400"
              placeholder="Поиск..."
            />
          </div>
        </div>
      </div>
      <div class="mt-10">
        <CartList :items="items" @add-to-favorite="addToFavorite" @add-to-cart="onClickAddToCart" />
      </div>
    </div>
  </div>
</template>

<style scoped></style>
