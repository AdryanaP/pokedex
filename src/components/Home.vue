<template>
  <header>
    <h1>Pokédex</h1>
    <img
      src="@/assets/pokeball.png"
      alt="marca d'agua pokebola"
      id="img-pokeball"
    />
    <form id="search-pokemon">
      <input type="text" v-model="searchPokemon" />
      <button @click.prevent="getPokemon(searchPokemon)" type="submit">
        <font-awesome-icon
          icon="fa-solid fa-magnifying-glass"
          size="lg"
          style="color: white"
        />
      </button>
    </form>
  </header>
  <body>
    <div v-if="pokemon.status">
      <div id="searched-pokemon" class="card-pokemon">
        <img :src="pokemon.img" alt="imagem pokemon" class="img-pokemon" />
        <div>
          <div id="header-card">
            <h2 class="name-pokemon">{{ pokemon.name }}</h2>
            <span
              class="type-pokemon"
              v-for="type in pokemon.types"
              :key="type"
              >{{ type }}</span
            >
          </div>

          <div id="attr-pokemon">
            <p><span class="attr-title">HP:</span> {{ pokemon.attr.hp }}</p>
            <p>
              <span class="attr-title">Speed:</span> {{ pokemon.attr.speed }}
            </p>
            <p>
              <span class="attr-title">Attack:</span> {{ pokemon.attr.attack }}
            </p>
            <p>
              <span class="attr-title">Special Attack:</span>
              {{ pokemon.attr.specialAttack }}
            </p>
            <p>
              <span class="attr-title">Defense:</span>
              {{ pokemon.attr.defense }}
            </p>
            <p>
              <span class="attr-title">Special Defense:</span>
              {{ pokemon.attr.specialDefense }}
            </p>
          </div>
        </div>
      </div>
      <h4 v-if="evolutionStage1.length || evolutionStage2.length">
        Evoluções:
      </h4>
      <section id="section-evolutions">
        <card-pokemon
          v-for="evolution in evolutionStage1"
          :key="evolution.species.name"
          :pokemonName="evolution.species.name"
          @click.prevent="getPokemon(evolution.species.name)"
        />
        <card-pokemon
          v-for="evolution in evolutionStage2"
          :key="evolution.species.name"
          :pokemonName="evolution.species.name"
          @click.prevent="getPokemon(evolution.species.name)"
        />
      </section>
    </div>
    <error-warning :error="error" />
  </body>
</template>

<script>
import CardPokemon from "@/components/CardPokemon";
import ErrorWarning from "./ErrorWarning.vue";

export default {
  name: "Index",
  props: {
    msg: String,
  },
  components: { CardPokemon, ErrorWarning },
  data() {
    return {
      searchPokemon: "",
      pokemon: {
        name: "",
        img: "",
        attr: {
          hp: 0,
          attack: 0,
          defense: 0,
          specialAttack: 0,
          specialDefense: 0,
          speed: 0,
        },
        types: [],
        status: false,
      },
      evolutionStage1: [],
      evolutionStage2: [],
      error: false,
      evolutionLink: "",
    };
  },
  methods: {
    getPokemon(pokemonName) {
      pokemonName = pokemonName.toLowerCase();
      fetch(`https://pokeapi.co/api/v2/pokemon/${pokemonName}/`, {
        method: "GET",
      })
        .then((res) => res.json())
        .then((res) => {
          // Resetando dados
          this.pokemon.types = [];
          this.error = false;

          // Coletando dados da resposta
          this.pokemon.name = res.name;
          this.pokemon.img = res.sprites.front_default;
          this.pokemon.status = true;
          this.pokemon.attr.hp = res.stats[0].base_stat;
          this.pokemon.attr.attack = res.stats[1].base_stat;
          this.pokemon.attr.defense = res.stats[2].base_stat;
          this.pokemon.attr.specialAttack = res.stats[3].base_stat;
          this.pokemon.attr.specialDefense = res.stats[4].base_stat;
          this.pokemon.attr.speed = res.stats[5].base_stat;
          res.types.forEach((type) => {
            this.pokemon.types.push(type.type.name);
          });

          this.error = false;
          this.getSpecies(res.name);
        })
        .catch(() => {
          // Setando aviso de erro
          this.error = true;
          setTimeout(() => {
            this.error = false;
          }, 5000);
        });
    },
    getSpecies(name) {
      fetch(`https://pokeapi.co/api/v2/pokemon-species/${name}`, {
        method: "GET",
      })
        .then((res) => res.json())
        .then((res) => {
          this.evolutionLink = res.evolution_chain.url;
          this.getEvolutions(name);
        });
    },
    getEvolutions(pokemonName) {
      // Resetando dados
      this.evolutionStage1 = [];
      this.evolutionStage2 = [];

      fetch(this.evolutionLink, {
        method: "GET",
      })
        .then((res) => res.json())
        .then((res) => {
          // Verificando se o pokemon tem evoluções
          if (
            res.chain.evolves_to.length === 1 &&
            res.chain.evolves_to[0].evolves_to.length > 0
          ) {
            const stage1Name = res.chain.evolves_to[0].species.name;
            const stage2Name =
              res.chain.evolves_to[0].evolves_to[0].species.name;
            // Verificando se o pokemon de evolução é o mesmo que o pesquisado
            if (pokemonName === stage1Name) {
              this.evolutionStage2 = res.chain.evolves_to[0].evolves_to;
            } else if (pokemonName != stage2Name) {
              this.evolutionStage1 = res.chain.evolves_to;
              this.evolutionStage2 = res.chain.evolves_to[0].evolves_to;
            }
          } else if (pokemonName != res.chain.evolves_to[0].species.name) {
            this.evolutionStage1 = res.chain.evolves_to;
          }
        });
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@400;700&display=swap");
@import url("http://fonts.cdnfonts.com/css/pok");

html {
  background: #6a3093;
  background: -webkit-linear-gradient(to right, #a044ff, #6a3093);
  background: linear-gradient(to right, #a044ff, #6a3093);
  background-repeat: no-repeat;
  font-family: "Poppins", sans-serif;
  color: white;
}

body {
  margin: 0;
}

#app {
  min-height: 100vh;
}

h1,
body,
#search-pokemon {
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 0;
}

h1 {
  font-family: "Pokemon Hollow", sans-serif;
  font-size: 48px;
}

header {
  margin: 1rem;
}

#img-pokeball {
  position: absolute;
  top: 0rem;
  left: 1rem;
  width: 20rem;
  opacity: 0.3;
  z-index: -1;
}

#search-pokemon {
  display: flex;
  margin: 1rem;
  gap: 1rem;
}

#search-pokemon input {
  padding: 0.5rem;
  border: 0;
  border-bottom: 2px solid white;
  background: transparent;
  outline: none;
  color: white;
}

#search-pokemon button {
  background: transparent;
  border: 0;
  cursor: pointer;
}

#section-evolutions {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 3rem;
  margin-bottom: 2rem;
}

.card-pokemon {
  display: flex;
  align-items: center;
  justify-content: space-between;
  border-radius: 20px;
  width: 16rem;
  margin: 1rem auto;
  box-shadow: rgba(0, 0, 0, 0.24) 0px 3px 8px;
  background: #ffffff2e;
}

#searched-pokemon .card-pokemon {
  padding: 2rem 0rem 0rem 2rem;
}

#searched-pokemon .img-pokemon {
  width: 18vw;
}

#searched-pokemon {
  width: 34rem;
}

#header-card {
  display: flex;
  align-items: center;
  justify-content: flex-start;
  gap: 1rem;
}

.name-pokemon {
  text-transform: capitalize;
}

#attr-pokemon {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  background: #ffffff1f;
  padding: 1rem 2rem 1rem 2rem;
  border-radius: 10px 0 20px 0;
}

.attr-title {
  font-weight: bold;
}

.type-pokemon {
  background: #f8f8f838;
  padding: 0.3rem 0.8rem;
  border-radius: 15px;
  font-size: 14px;
  text-transform: capitalize;
  color: white;
}

.img-pokemon {
  width: 12vw;
}

h4 {
  text-align: center;
  font-size: 24px;
  margin-top: 2rem;
}

@media (max-width: 730px) {
  h1 {
    font-size: 39px;
  }
  #section-evolutions {
    grid-template-columns: auto;
  }
  .card-pokemon {
    flex-direction: column;
    padding: 1rem;
    margin: 1rem;
    width: auto;
  }
  #searched-pokemon {
    width: auto;
  }
  #img-pokeball {
    width: 10rem;
  }
  .img-pokemon {
    width: 40vw;
  }
  #searched-pokemon .img-pokemon {
    width: 50vw;
  }
  #attr-pokemon {
    background: transparent;
    padding: 0;
  }
}
</style>
