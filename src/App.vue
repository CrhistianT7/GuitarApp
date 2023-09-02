<script setup lang="ts">
import { ref, onMounted, watch } from 'vue'
import { db } from './data/guitars'
import { DBProps } from './data/guitars'
import Guitar from './components/Guitar.vue'
import Header from './components/Header.vue'
import Footer from './components/Footer.vue'

const guitars = ref(db)
const cart = ref([])
const mainGuitar = ref({})

watch(cart, () => {
    saveLocalStorage()
}, {
    deep: true
})

onMounted(() => {
    mainGuitar.value = db[3]
    const savedItems = localStorage.getItem('cart')
    if(savedItems) {
        cart.value = JSON.parse(savedItems)
    }
})

const saveLocalStorage = () => {
    localStorage.setItem('cart', JSON.stringify(cart.value))
}

const addCart = (guitar: DBProps) => {
    const index = cart.value.findIndex(product => product.id === guitar.id)
    if (index === -1) {
        guitar.cantidad = 1
        cart.value.push(guitar)
    } else {
        cart.value[index].cantidad += 1
    }
}

const removerCart = (id: number) => {
    cart.value = cart.value.filter(product => product.id !== id)
}

const emptyCart = () => cart.value = []

const increaseAmount = (id: number) => {
    const index = cart.value.findIndex(product => product.id === id)
    if (cart.value[index].cantidad >= 5) return
    cart.value[index].cantidad++
}

const decreaseAmount = (id: number) => {
    const index = cart.value.findIndex(product => product.id === id)
    if (cart.value[index].cantidad <= 1) return
    cart.value[index].cantidad--
}

</script>

<template>
    <Header v-bind:cart="cart" @increase-amount="increaseAmount" @decrease-amount="decreaseAmount" :guitar="mainGuitar" @add-cart="addCart" @remove-cart="removerCart" @empty-cart="emptyCart"/>
    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>

        <div class="row mt-5">
            <Guitar v-for="guitar in guitars" v-bind:guitar="guitar" @add-cart="addCart" />
        </div>
    </main>
    <Footer />
</template>

<style scoped></style>
