<template>
  <header>
    <h1>Encontre seu pokemon</h1>
    <form class="search-pokemon">
      <input type="text" v-model="searchPokemon" />
      <button @click.prevent="getPokemon" type="submit">Pesquisar</button>
    </form>
  </header>
  <body>
    <div v-if="pokemon.status">
      <div class="card-pokemon">
        <img :src="pokemon.img" alt="imagem pokemon" class="img-pokemon" />
        <h3>{{ pokemon.name }}</h3>
      </div>
      <h4 v-if="evolutionStage1.length || evolutionStage2.length ">Evoluçoes:</h4>
      <section class="section-evolutions">
        <CardPokemon v-for="evolution in evolutionStage1" :key="evolution.species.name" 
        :pokemonName="evolution.species.name"/>
        <CardPokemon v-for="evolution in evolutionStage2" :key="evolution.species.name" 
        :pokemonName="evolution.species.name"/>
      </section>
    </div>
  </body>
</template>

<script>
import CardPokemon from "@/components/CardPokemon";

export default {
  name: "Index",
  props: {
    msg: String,
  },
  components: {CardPokemon},
  data() {
    return {
      searchPokemon: "",
      pokemon: {
        name: "",
        img: "",
        status: false,
      },
      evolutionStage1: [],
      evolutionStage2: [],
      error: false,
      errorMsg: "",
      evolutionLink: ""
    };
  },
  methods: {
    getPokemon() {
      fetch(`https://pokeapi.co/api/v2/pokemon/${this.searchPokemon}/`, {
        method: "GET"
      })
        .then((res) => res.json())
        .then((res) => {
          this.pokemon.name = res.name;
          this.pokemon.img = res.sprites.front_default;
          this.pokemon.status = true;
          this.getSpecies(res.name)
          console.log(res)
        })
        .catch((e) => {
          this.error = true;
          console.log(e);
        });
    },
    getSpecies(name) {
      fetch(`https://pokeapi.co/api/v2/pokemon-species/${name}`, {
        method: "GET"
      })
        .then((res) => res.json())
        .then((res) => {
          this.evolutionLink = res.evolution_chain.url
          this.getEvolutions() 
        })
    },
    getEvolutions() {
      this.evolutionStage1 = []
      this.evolutionStage2 = []
      fetch(this.evolutionLink, {
        method: "GET"
      })
        .then((res) => res.json())
        .then((res) => {
          if(res.chain.evolves_to.length === 1 && 
          res.chain.evolves_to[0].evolves_to.length > 0) {
            const stage1Name = res.chain.evolves_to[0].species.name
            const stage2Name = res.chain.evolves_to[0].evolves_to[0].species.name
            if(this.searchPokemon === stage1Name) {
              this.evolutionStage2 = res.chain.evolves_to[0].evolves_to
            } else if(this.searchPokemon != stage2Name) {
              this.evolutionStage1 = res.chain.evolves_to
              this.evolutionStage2 = res.chain.evolves_to[0].evolves_to
            } 
          } else if (this.searchPokemon != res.chain.evolves_to[0].species.name){
            this.evolutionStage1 = res.chain.evolves_to
          }
        })
    }
  },
};
</script>

<style>
h1,
body,
.search-pokemon,
.section-evolutions {
  display: flex;
  justify-content: center;
  align-items: center;
}

.card-pokemon {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 1rem;
  padding: 2rem;
  border: 3px solid black;
  border-radius: 10px;
}

.img-pokemon {
  width: 12vw;
}
</style>
