<template>
  <div class="card-pokemon card-evolution">
      <img :src="pokemon.img" alt="imagem pokemon" class="img-pokemon" />
      <h3 class="name-pokemon">{{ pokemon.name }}</h3>
  </div>
</template>

<script>
export default {
  title: "CardPokemon",
  props: {
    pokemonName: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      pokemon: {
        name: this.pokemonName,
        img: "",
      },
    }
  },
  created() {
    fetch(`https://pokeapi.co/api/v2/pokemon/${this.pokemonName}/`, {
      method: "GET"
    })
      .then((res) => res.json())
      .then((res) => {

        this.pokemon.img = res.sprites.front_default;
      })
  }
}
</script>

<style>
.card-evolution {
  cursor: pointer;
  padding: 2rem;
}
</style>