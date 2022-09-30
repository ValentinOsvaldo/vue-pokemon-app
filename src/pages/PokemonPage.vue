<template>
  <h1 v-if="!pokemon">Espere por favor...</h1>

  <div v-else>
    <h1>¿Quién es este pokemon?</h1>
    <!-- Imagen -->
    <PokemonPicture :pokemonId="pokemon.id" :showPokemon="showPokemon" />
    <!-- Opciones -->
    <PokemonOptions
      :pokemons="pokemonArr"
      :showOptions="!showAnswer"
      @selection="checkAnswer"
    />
    <template v-if="showAnswer">
      <h2 class="fade-in">{{ message }}</h2>
    </template>
    <h1>Puntuaci&oacute;n: {{ score }} puntos</h1>
    <h3>Record: {{ highScore }} puntos</h3>
    <button @click="newGame">Reiniciar juego</button>
  </div>
</template>

<script>
import PokemonOptions from '@/components/PokemonOptions.vue';
import PokemonPicture from '@/components/PokemonPicture.vue';

import getPokemonOptions from '@/helpers/getPokemonsOptions';

export default {
  components: { PokemonPicture, PokemonOptions },
  data() {
    return {
      pokemonArr: [],
      pokemon: null,
      showPokemon: false,
      showAnswer: false,
      message: '',
      score: 0, // Added a score to data so we can play more thank once. rPerez
      highScore: 0, // Added so we can show the highScore, used localStorage to save it
      turn: 1, // Added turn so we can determine when the game is over, as opt-in the user can restart the game at anytime
    };
  },
  methods: {
    async mixPokemonArray() {
      this.pokemonArr = await getPokemonOptions();

      const rndInt = Math.floor(Math.random() * 4); // Generando un numero aleatorio

      this.pokemon = this.pokemonArr[rndInt];
    },
    checkAnswer(selectedId) {
      this.showPokemon = true;
      this.showAnswer = true;
      // we set the turn to turn+1
      this.turn = Number.parseInt(this.turn) + 1;
      if (selectedId === this.pokemon.id) {
        this.message = `Correcto, ${this.pokemon.name}`;
        // Once we checked its an correct answer we add 15 to the current score
        this.score = Number.parseInt(this.score) + 15;
        localStorage.setItem('score', this.score);
      } else {
        this.message = `Oops, es ${this.pokemon.name}`;
      }
      setTimeout(this.nextPokemon, 2000);
    },
    nextPokemon() {
      this.showPokemon = false;
      this.showAnswer = false;
      if (this.turn == 10) {
        this.turn = 1;
        alert('Juego terminado');
        let oldHighScore = localStorage.getItem('highScore');
        if (this.score > oldHighScore) {
          alert(`Juego terminado \n Haz alcanzado un nuevo record!`);
          localStorage.setItem('highScore', this.score);
          this.highScore = this.score;
        } else {
          alert(
            `Juego terminado, no haz logrado batir tu record, sigue intentando!`
          );
        }
        this.score = 0;
        localStorage.removeItem('score');
      }
      this.pokemon = null;
      this.pokemonArr = [];
      this.mixPokemonArray();
    },
    newGame() {
      this.score = 0;
      localStorage.removeItem('score');
      this.nextPokemon();
    },
  },
  mounted() {
    this.mixPokemonArray();
    if (localStorage.getItem('highScore') !== null) {
      this.highScore = localStorage.getItem('highScore');
    }
  },
  watch: {
    showAnswer() {
      console.log('Answer choosen');
      console.log(this.showAnswer);
    },
    pokemonArr() {},
  },
};
</script>

<style scoped>
button {
  background-color: cornflowerblue;
  border: thin solid cornflowerblue;
  border-radius: 0.25rem;
  color: #fff;
  cursor: pointer;
  font-size: 1rem;
  font-weight: 500;
  padding: 0.5rem 1rem;
  transition: all 0.3s ease;
}

button:hover {
  background-color: rgb(100, 149, 237, 0.10);
  color: #000;
}
</style>
