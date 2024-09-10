<script>
import Pokemon from "./components/Pokemon.vue"; // Importamos el componente hijo Pokemon
import axios from "axios"; // Importamos Axios para realizar solicitudes HTTP

export default {
  name: "App", // Nombre del componente
  components: {
    Pokemon, // Registramos el componente hijo Pokemon
  },
  data() {
    return {
      pokemones: [], // Array que almacenará la lista de pokemones
      contador: 0, // Contador para los pokemones descubiertos
    };
  },
  methods: {
    // Método asíncrono para obtener los pokemones desde la API
    async getPokemons() {
      try {
        /* https://pokeapi.co/api/v2/pokemon?offset=0&limit=20 --> url alternativa que permite ajustar el límite de pokemones con offset y limit si es necesario */
        // URL de la API de Pokémon usada
        const url = "https://pokeapi.co/api/v2/pokemon";
        // Hacemos la solicitud GET a la API y guardamos los resultados
        const { data } = await axios.get(url);
        this.pokemones = data.results; // Asignamos los resultados obtenidos al array `pokemones`
      } catch (error) {
        console.error("Error al obtener los datos:", error); // Manejo de errores si la API falla
      }
    },
    // Método para aumentar el contador de pokemones descubiertos
    aumentar() {
      this.contador++; // Incrementa en 1 el contador
    }
  },
  mounted() {
    // Llamamos a `getPokemons` cuando el componente se monta, es decir, cuando se carga la página
    this.getPokemons();
  },
};
</script>

<!-- Template HTML -->
<template>
  <header class="container text-center">
    <!-- Imagen del logo de Pokémon -->
    <img class="w-50 my-3" src="./assets/img/pokemon_logo.png" alt="Logo de Pokémon">
    <!-- Título y contador de pokemones descubiertos -->
    <h1>¿Quién es ese Pokémon?</h1>
    <h4 class="mb-5">Pokemones descubiertos: <span class="text-warning">{{ contador }}</span></h4>
  </header>

  <main class="container my-4">
    <!-- Si ya tenemos pokemones en el array, renderizamos la lista -->
    <div class="row g-4" v-if="pokemones.length">
      <!-- Iteramos sobre el array `pokemones` usando v-for -->
      <div class="col-12 col-sm-6 col-md-4 col-lg-3" v-for="(pokemon, index) in pokemones" :key="index">
        <!-- Pasamos cada pokemon al componente hijo Pokemon y escuchamos el evento `pokemonDescubierto` -->
        <Pokemon :pokemon="pokemon" @pokemonDescubierto="aumentar" />
      </div>
    </div>
    <!-- Si no hay pokemones (todavía no cargan), mostramos un mensaje y un spinner de carga -->
    <div class="text-center" v-else>
      <h2>Cargando Pokemones...</h2>
      <!-- Spinners para indicar que la aplicación está cargando -->
      <div class="spinner-grow text-primary spinner m-5" role="status"></div>
      <div class="spinner-grow text-primary spinner m-5" role="status"></div>
      <div class="spinner-grow text-primary spinner m-5" role="status"></div>
      <div class="spinner-grow text-primary spinner m-5" role="status"></div>
    </div>
  </main>
</template>

<!-- Style CSS -->
<style scoped>
/* Estilos personalizados para los spinners de carga */
.spinner {
  width: 100px;
  height: 100px;
}
</style>
