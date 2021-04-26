<template>
  <div id="app">
    <img width="300px" alt="Vue logo" src="https://upload.wikimedia.org/wikipedia/commons/thumb/9/98/International_Pok%C3%A9mon_logo.svg/1200px-International_Pok%C3%A9mon_logo.svg.png">
    <h1>PokeGuía</h1>
    <div class="poke-form">
      <p>Nombre</p>
      <input v-model="pokeToFind"/>
      <button @click="onFindPokemon(pokeToFind)">Buscar</button>
    </div>
    <img :src="computedPokemonImage"  />
    <h3 v-show="movimientos.lenght!=0">Movimientos</h3>
    <ul><li v-for="(mov,index) in computedMovimientos" :key="index">{{mov}}</li></ul>
    <h3 v-show="habilidades.lenght!=0">Habilidades</h3>
    <ul><li v-for="(hab,index) in computedHabilidades" :key="index">{{hab}}</li></ul>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'App',
  data() {
    return {
      pokeToFind: '',
      pokeImage: '',
      movimientos: [],
      habilidades: []
    }
  },
  methods: {
    async onFindPokemon(pokemonToFind) {
      try{
        if(!pokemonToFind || pokemonToFind.trim==='' || pokemonToFind == null)
          throw 'Invalid pokemon, can be empty'
        const url = `https://pokeapi.co/api/v2/pokemon/${pokemonToFind}`;
        let pokemon = await axios.get(url);
        this.setupPokemon(pokemon);
      }catch(e){
        console.log(`Poke error: ${e}`);
        this.clearPokemon();
        alert("Algo salió mal mientras se buscaba al pokemon")
      }
    },
    clearPokemon(){
      this.pokeToFind = '';
      this.movimientos = [];
      this.habilidades = [];
      this.pokeImage = '';
    },
    setupPokemon(pokemon){
      console.log(pokemon)
      this.habilidades = pokemon.data.abilities.map(hab => hab.ability.name);
      this.movimientos = pokemon.data.moves.map(mov => mov.move.name);
      this.pokeImage = pokemon.data.sprites.front_default;
    }
  },
  created(){
    this.onFindPokemon("pikachu");
  },
  computed: {
    computedHabilidades() {
      return this.habilidades.slice(0,5);
    },
    computedMovimientos(){
      return this.movimientos.slice(0,5);
    },
    computedPokemonImage(){
      return this.pokeImage;
    }
  },
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

li {
  list-style: none;
}

.poke-form{
  display: flex;
  justify-content: center;
  align-items:  center;
}
</style>
