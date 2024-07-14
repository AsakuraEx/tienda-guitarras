<script setup>
  
  //SECCION DE LAS IMPORTACIONES
  import {onMounted, ref, watch} from 'vue';
  import {db} from './data/guitarras';
  import Guitarra from './components/Guitarra.vue';
  import Header from './components/Header.vue';
  import Footer from './components/Footer.vue';

  //VARIABLES DECLARADAS COMO ARRAY
  const guitarras = ref([]);
  const carrito = ref([]);
  const guitarra = ref([]);

  // FUNCIONES QUE SE INICIALIZAN AL CARGAR LA PAGINA

  watch(carrito, () => {
      guardarLocalStorage();
    }, {
      deep: true
    }
  );

  onMounted(()=>{
    guitarras.value = db;
    guitarra.value = db[3];

    const carritoStorage = localStorage.getItem('carrito');
    if(carritoStorage){
      carrito.value = JSON.parse(carritoStorage);
    }
  });


  // FUNCIONES DEFINIDAS POR MI
  const guardarLocalStorage = () => {
    localStorage.setItem('carrito', JSON.stringify(carrito.value)); //Almacena los valores por si se refresca la pagina
  }

  const agregarCarrito = ((guitarra) =>{
    const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id);
    if(existeCarrito >= 0){
      carrito.value[existeCarrito].cantidad++;
    } else {
      guitarra.cantidad = 1;
      carrito.value.push(guitarra);
    }


  });

  //Esta es la version del profesor
  const decrementarCantidad = ((guitarra) => {
    
    const index = carrito.value.findIndex(producto => producto.id === guitarra.id);

    if(carrito.value[index].cantidad > 1){
      carrito.value[index].cantidad--;
    }else{
      carrito.value[index].cantidad = 1;
    }

  });

  //Esta es mi version XD
  const incrementarCantidad = (guitarra) => {
    if(guitarra.cantidad < 5){
      guitarra.cantidad++;
    }else{
      guitarra.cantidad = guitarra.cantidad;
    }

  };

  const eliminarGuitarra = ((id) =>{
      carrito.value = carrito.value.filter(producto => producto.id !== id); //busca los id diferentes y los guarda, para solo eliminar el que es igual

  });

  const vaciarCarrito = (()=>{
      carrito.value = [];

  });

</script>

<template>
  
  <Header 
    :carrito="carrito"
    :guitarra="guitarra"
    @decrementar-cantidad = "decrementarCantidad"
    @incrementar-cantidad = "incrementarCantidad"
    @agregar-carrito="agregarCarrito"
    @eliminar-guitarra="eliminarGuitarra"
    @vaciar-carrito="vaciarCarrito"
  />

  <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>

        <div class="row mt-5">

          <Guitarra 
            v-for="guitarra in guitarras"
            :guitarra="guitarra"
            @agregar-carrito="agregarCarrito"
          />

        </div>
  </main>

  <Footer />

</template>