<template>
  <v-app>
    <!-- Banner Section -->
    <BannerBar></BannerBar>

    <!-- Search Field -->
    <v-container>
      <v-text-field v-model="search" label="Buscar" placeholder="Charmander" solo></v-text-field>
      <v-row justify="center">
        <v-col cols="12" sm="6" md="4" lg="3" v-for="pokemon in filtered_pokemons" :key="pokemon.name">
          <!-- Pokemon Card -->
          <v-card @click="showPokemon(pokemon.id)" class="mb-4">
            <v-container>
              <v-row>
                <v-col cols="12">
                  <img :src="pokemon.imageUrl" :alt="pokemon.name" class="w-100">
                </v-col>
              </v-row>
              <v-row>
                <v-col cols="12">
                  <h2 class="text-h6 text-center">{{ pokemon.name }}</h2>
                  <p class="text-caption text-center">Type: {{ pokemon.types }}</p>
                  <p class="text-caption text-center">Weight: {{ (pokemon.weight * 0.45359237).toFixed(0) }} kg</p>
                </v-col>
              </v-row>
            </v-container>
          </v-card>
        </v-col>
      </v-row>
    </v-container>

    <!-- Pokemon Details Dialog -->
    <v-dialog v-model="showDialog" width="800">
      <v-card v-if="selectedPokemon" class="px-4">
        <v-container class="px-2 text-center">
          <v-row class="d-flex align-center">
            <v-col cols="4">
              <img :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${selectedPokemon.id}.png`" :alt="selectedPokemon.name" width="80%" />
            </v-col>
            <v-col cols="8">
              <h1>{{ capitalize(selectedPokemon.name) }}</h1>
              <v-chip label v-for="type in selectedPokemon.types" :key="type.slot" class="mr-2">{{ type.type.name }}</v-chip>
              <v-divider class="my-4"></v-divider>
              <v-chip label>Altura {{ selectedPokemon.height * 2.54 }} cm</v-chip>
              <v-chip label class="ml-2">Peso {{ (selectedPokemon.weight * 0.45359237).toFixed(0) }} kg</v-chip>
              <v-divider class="my-4"></v-divider>
              <h3>Descripcion</h3>
              <p>{{ selectedPokemonDescription }}</p>
              <v-divider class="my-4"></v-divider>
            </v-col>
          </v-row>
          <h2>Moves</h2>
          <v-table>
            <template>
              <tbody v-for="item in selectedPokemon.moves" :key="item.move.name">
                <tr>
                  <td>{{ item.move.name }}</td>
                </tr>
              </tbody>
            </template>
          </v-table>
        </v-container>
      </v-card>
    </v-dialog>
    <v-container v-if="error" class="mt-4">
      <v-alert color="error">{{ error }}</v-alert>
    </v-container>
  </v-app>
</template>

<script>
import axios from 'axios';
import BannerBar from './components/BannerBar.vue'

export default {
  name: 'App',
  components: {
    BannerBar
  },
  data() {
    return {
      pokemons: [],
      search: '',
      showDialog: false,
      selectedPokemon: null,
      selectedPokemonDescription: '',
      error: null,
    };
  },

  mounted() {
    axios.get('https://pokeapi.co/api/v2/pokemon?limit=152').then((res) => {
      const pokemonList = res.data.results;
      const pokemonPromises = pokemonList.map((pokemon) => axios.get(pokemon.url));

      Promise.all(pokemonPromises)
        .then((responses) => {
          this.pokemons = responses.map((response) => {
            const data = response.data;
            return {
              name: this.capitalize(data.name),
              id: data.id,
              imageUrl: `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${data.id}.png`,
              types: this.getTypes(data),
              weight: this.formatWeight(data.weight),
            };
          });
        })
        .catch((error) => {
          throw new Error(error)
        });
    });
  },
  methods: {
    getTypes(pokemon) {
      if (pokemon.types && pokemon.types.length > 0) {
        return pokemon.types.map((type) => type.type.name).join(', ');
      }
      return 'N/A';
    },

    formatWeight(weight) {
      if (weight !== 'N/A') {
        return (weight * 0.1).toFixed(1);
      }
      return 'N/A';
    },

    capitalize(str) {
      return str.charAt(0).toUpperCase() + str.slice(1);
    },

    showPokemon(id) {
      axios.get(`https://pokeapi.co/api/v2/pokemon/${id}`).then((res) => {
        this.selectedPokemon = res.data;
        this.fetchPokemonDescription(id);
        this.showDialog = true;
      }).catch((error) => {
        // Manejo de errores de la solicitud de detalles del Pokémon
        throw new Error(error)
      });
    },

    fetchPokemonDescription(id) {
      axios.get(`https://pokeapi.co/api/v2/pokemon-species/${id}`).then((res) => {
        const speciesData = res.data;
        const descriptionEntry = speciesData.flavor_text_entries.find((entry) => entry.language.name === 'es');
        this.selectedPokemonDescription = descriptionEntry ? descriptionEntry.flavor_text : 'No description available.';
      }).catch((error) => {
        // Manejo de errores de la solicitud de descripción
        throw new Error(error)
      });
    },
  },

  computed: {
    filtered_pokemons() {
      if (this.search === '') {
        return this.pokemons;
      } else {
        return this.pokemons.filter((item) => {
          return item.name.includes(this.search);
        });
      }
    },
  },
};
</script>

<style>
#app {
  background: linear-gradient(
    to bottom right,
    rgba(10, 10, 10, 1),
    rgba(12, 39, 63, 1)
  ) no-repeat center center fixed ;
  background-size: cover ;
  background-position: center;
  min-height: 100vh;
}

.banner {
  background: linear-gradient(
    to bottom right,
    rgba(10, 10, 10, 1),
    rgba(12, 39, 63, 1)
  ) no-repeat center center fixed ;
  background-size: cover;
  background-position: center;
  min-height: 100vh;
}

.banner-subtitle {
  font-size: 0.3em;
}
</style>
