<script setup>
    import {ref, reactive, onMounted, watch} from'vue'
    import { db } from './data/guitarras'
    import Guitarra from './components/Guitarra.vue'
    import Header from './components/Header.vue'
    import Footer from './components/Footer.vue'

    const guitarras = ref([])
    const carrito = ref([])
    const guitarra = ref({})

    watch(carrito, ()=>{
        //console.log('carrito cambio')
        guardarLocalStorage()
    },{
        deep:true
    })

    //console.log(guitarras.value)
    onMounted(()=>{
        //console.log('component ready...');
        guitarras.value = db;
        guitarra.value = db[3];

        const carritoStorage = localStorage.getItem('carrito')
        if(carrito){
            carrito.value = JSON.parse(carritoStorage)
        }
    })

    const addCar = (guitarra)=>{
        //console.log(guitarra)
        const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id)
        //console.log(existeCarrito);// returns position  or -1 if not found
        if(existeCarrito >= 0){
            carrito.value[existeCarrito].cantidad++
        }
        else{
            guitarra.cantidad = 1
            carrito.value.push(guitarra)
        }
    }

    const incrementarCantidad = (id) => {
        //console.log('incrementar');
        const index = carrito.value.findIndex(producto => producto.id === id)
        if(carrito.value[index].cantidad >= 5) return
        carrito.value[index].cantidad++
    }

    const decrementarCantidad = (id) =>{
        //console.log('decrement');
        const index = carrito.value.findIndex(producto => producto.id === id)
        if(carrito.value[index].cantidad <= 1) return
            carrito.value[index].cantidad--
    }

    const eliminarProducto = (id) =>{
        carrito.value = carrito.value.filter(producto => producto.id !== id)
    }

    const vaciarCarrito = () => carrito.value = []

    const guardarLocalStorage = () =>{
        localStorage.setItem('carrito',JSON.stringify(carrito.value))
    }

</script>

<template>
    <Header
    :carrito="carrito"
    :guitarra="guitarra"
    @decrementar-cantidad="decrementarCantidad"
    @incrementar-cantidad="incrementarCantidad"
    @add-car="addCar"
    @eliminar-producto="eliminarProducto"
    @vaciar-carrito="vaciarCarrito"
    />
    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>
        <div class="row mt-5">
           <Guitarra
                v-for="guitarra in guitarras"
                :key="guitarra.id"
                v-bind:guitarra = "guitarra"
                @add-car = "addCar"
           />
        </div>
    </main>
    <Footer/>
</template>