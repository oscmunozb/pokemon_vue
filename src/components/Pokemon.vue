<script>
import axios from "axios"; // Importamos Axios para realizar peticiones HTTP

export default {
    name: "Pokemon", // Nombre del componente
    props: {
        // Definimos la prop `pokemon`, que es un objeto con los datos del Pokémon
        pokemon: Object,
    },
    data() {
        return {
            dataPokemon: {}, // Objeto que almacenará los detalles del Pokémon
            oculto: true, // Controla si el Pokémon está oculto o visible
            adivinarPokemon: "", // Almacena el nombre que el usuario ingresa para adivinar el Pokémon
        }
    },
    methods: {
        // Método asíncrono para obtener los detalles del Pokémon a través de la URL proporcionada
        async getDataPokemon() {
            try {
                // Petición a la API para obtener detalles del Pokémon usando su URL
                let { data } = await axios.get(this.pokemon.url)
                this.dataPokemon = data; // Almacenamos los datos obtenidos en `dataPokemon`
            } catch (error) {
                console.log(error); // Si ocurre un error, lo mostramos en la consola
            }
        },
        // Método para descubrir el Pokémon basado en el nombre ingresado
        descubrir() {
            // Comparamos el nombre ingresado con el nombre del Pokémon
            if (this.adivinarPokemon.toLowerCase() === this.pokemon.name.toLowerCase()) {
                this.oculto = false; // Si es correcto, mostramos el Pokémon
                // Emitimos un evento al componente padre para aumentar el contador
                this.$emit("pokemonDescubierto");
            } else if (this.adivinarPokemon.toLowerCase() === "") {
                // Si no se ingresó ningún nombre, mostramos una alerta
                alert(`Ingresa el nombre del pokémon...`);
            } else {
                // Si el nombre es incorrecto, mostramos una pista
                const name = this.pokemon.name;
                alert(`Te equivocaste, este pokémon no es: ${this.adivinarPokemon} \nPista: el nombre del pokemon comienza con: *${name.substring(0, 1)}* y termina con: *${name.substring(name.length - 1)}*`);
            }
        }
    },
    computed: {
        // Computed property para obtener la imagen del Pokémon (puede ser `undefined`, por eso usamos el operador `?.`)
        imgPokemon() {
            // Usamos el operador `?.` para evitar errores si `sprites` o `other` son undefined
            return this.dataPokemon.sprites?.other.dream_world.front_default;
        },
        // Computed property para obtener el nombre del Pokémon en mayúsculas
        nombreConMayuscula() {
            return this.pokemon.name.toUpperCase();
        }
    },
    // Ciclo de vida: al montar el componente, llamamos a `getDataPokemon` para cargar los datos
    mounted() {
        this.getDataPokemon();
    }
}
</script>

<!-- Template HTML -->
<template>
    <div class="card border-0 mb-3">
        <!-- Imagen del Pokémon con clases condicionales para aplicar filtro si está oculto -->
        <img class="card-img-top" :src="imgPokemon" :alt="pokemon.name" :class="oculto ? 'filtro' : 'visible'">
        <div class="card-body p-0 text-center">
            <!-- Mostrar el nombre del Pokémon solo si no está oculto -->
            <h3 class="card-title" v-show="!oculto">{{ nombreConMayuscula }}</h3>
            <!-- Formulario para adivinar el nombre del Pokémon si está oculto -->
            <form v-show="oculto">
                <input class="form-control w-50 mx-auto my-2" type="text" v-model="adivinarPokemon">
                <button class="btn btn-danger" @click.prevent="descubrir">Descubrir</button>
            </form>
        </div>
    </div>
</template>

<!-- Style CSS -->
<style scoped>
/* Clase aplicada cuando el Pokémon ha sido descubierto */
.visible {
    height: 160px; /* Aumenta la altura cuando se descubre el Pokémon */
    transition: 600ms; /* Transición suave al cambiar de tamaño */
}

/* Clase aplicada cuando el Pokémon está oculto */
.filtro {
    filter: blur(5px) grayscale(100%); /* Aplicamos un filtro de desenfoque y escala de grises */
    height: 110px; /* Menor altura mientras el Pokémon está oculto */
}
</style>
