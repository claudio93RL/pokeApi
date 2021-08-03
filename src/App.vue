<template>
  <div id="app">
    <div class="container">
      <img width="500px" src="../public/pokemon.png" alt="" />
      <h1 class="bold">PoKeGuía</h1>

      <form
        @submit.prevent="consultarPokemones"
        class="mt-5 form-inline justify-content-center"
      >
        <label>Nombre:</label>
        <input
          type="text"
          class="form-control mx-2"
          v-model.trim="nombrePokemon"
        />
        <button type="submit" class="btn btn-dark" :disabled="bloquearBoton">
          buscar
        </button>
      </form>
      <img :src="imagenPokemon" alt="" />
      <h2>movimientos</h2>
      <div v-for="item in movimientosPokemon" :key="item.id">
        {{ item.move.name }}
      </div>
      <h2>habilidades</h2>
      <div v-for="item in habilidaPokemon" :key="item.id">
        {{ item.ability.name }}
      </div>
    </div>
  </div>
</template>

<script>
import swal from "sweetalert";
export default {
  name: "App",
  data() {
    return {
      nombrePokemon: "",
      dataPokemon: [],
    };
  },
  methods: {
    async consultarPokemones() {
      try {
        const respuesta = await fetch(
          `https://pokeapi.co/api/v2/pokemon/${this.nombrePokemon}`
        );
        if (respuesta.status === 404) {
          swal({
            title: "Error!",
            text: `${this.nombrePokemon} no es un nombre de pokémon válido`,
            icon: "error",
          });
          this.dataPokemon = [];
          this.nombrePokemon = "";
          return;
        }

        this.dataPokemon = await respuesta.json();
      } catch (error) {
        console.log(error);
      }
    },
  },
  computed: {
    imagenPokemon() {
      return (
        (this.dataPokemon.sprites && this.dataPokemon.sprites.front_default) ||
        ""
      );
    },
    habilidaPokemon() {
      return this.dataPokemon.abilities || [];
    },
    movimientosPokemon() {
      return this.dataPokemon.moves || [];
    },
    bloquearBoton() {
      return this.nombrePokemon.trim() === "" ? true : false;
    },
  },
  created() {
    this.nombrePokemon = "pikachu";
    this.consultarPokemones();
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
