<template>
  <v-app>

    <!-- Banner Section -->
    <BannerBar></BannerBar>

    <!-- Search Field -->
    <v-container>
      <SearchField :search="search" @update-search="UpdateSearch"></SearchField> <!-- Pass search prop and listen for update-search event -->
      
      <v-row justify="center">
         <!-- Pokemon Cards using PokemonCard component -->
         <v-row justify="center">
            <v-container >
              <v-row justify="center my-1">
                <v-btn @click="goToPage(currentPage - 1)" :disabled="currentPage === 1" class="mx-1">Anterior</v-btn>
                <v-btn @click="goToPage(currentPage + 1)" :disabled="currentPage === totalPages" class="mx-1">Siguiente</v-btn>
              </v-row>
                <PokemonCard :filteredPokemons="FilteredPokemons" @show-pokemon="ShowPokemon"></PokemonCard>
              <v-row justify="center mb-5">
                <v-btn @click="goToPage(currentPage - 1)" :disabled="currentPage === 1" class="mx-1">Anterior</v-btn>
                <v-btn @click="goToPage(currentPage + 1)" :disabled="currentPage === totalPages" class="mx-1">Siguiente</v-btn>
              </v-row>
            </v-container>
          </v-row>
      </v-row>
    </v-container>
    <!-- Pokemon Details Dialog -->
    <PokemonDetailDialog :showDialog.sync="showDialog" :selectedPokemon="selectedPokemon" :selectedPokemonDescription="selectedPokemonDescription"></PokemonDetailDialog>

    <v-container v-if="error" class="mt-4">
      <v-alert color="error">{{ error }}</v-alert>
    </v-container>
    <div v-if="loading" class="loading-screen">
      <v-progress-circular indeterminate color="primary"></v-progress-circular>
      <p>Loading Pokémon data. Please wait...</p>
    </div>
  </v-app>
</template>

<script>
import axios from 'axios';
import BannerBar from './components/BannerBar.vue'
import SearchField from './components/SearchField.vue'
import PokemonCard from './components/PokemonCard.vue'; 
import PokemonDetailDialog from './components/PokemonDetailDialog.vue'; 


export default {
  name: 'App',
  components: {
    BannerBar,
    SearchField,
    PokemonCard,
    PokemonDetailDialog,
  },
  data() {
    return {
      pokemons: [],
      search: '',
      showDialog: false,
      selectedPokemon: null,
      selectedPokemonDescription: '',
      error: null,
      loading: true,
      currentPage: 1,
      totalPages: 1,
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
            name: this.Capitalize(data.name),
            id: data.id,
            imageUrl: `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${data.id}.png`,
            types: this.GetTypes(data),
            weight: this.FormatWeight(data.weight),
          };
        });
        this.totalPages = Math.ceil(this.pokemons.length / 20); // Actualiza el número total de páginas
        this.loading = false;
      })
      .catch((error) => {
        this.loading = false;
        throw new Error(error);
      });
    });
  },
  methods: {
    GetTypes(pokemon) {
      if (pokemon.types && pokemon.types.length > 0) {
        return pokemon.types.map((type) => type.type.name).join(', ');
      }
      return 'N/A';
    },
    UpdateSearch(newSearch) {
      this.search = newSearch; // Update the search variable in the parent component
    },
    FormatWeight(weight) {
      if (weight !== 'N/A') {
        return (weight);
      }
      return 'N/A';
    },

    Capitalize(str) {
      return str.charAt(0).toUpperCase() + str.slice(1);
    },

    ShowPokemon(id) {
      axios.get(`https://pokeapi.co/api/v2/pokemon/${id}`).then((res) => {
      this.selectedPokemon = res.data;
      this.FetchPokemonDescription(id);
      this.showDialog = true; // Show the dialog when a Pokemon is clicked
        }).catch((error) => {
          throw new Error(error);
        });
      },
    FetchPokemonDescription(id) {
      axios.get(`https://pokeapi.co/api/v2/pokemon-species/${id}`).then((res) => {
        const speciesData = res.data;
        const descriptionEntry = speciesData.flavor_text_entries.find((entry) => entry.language.name === 'es');
        this.selectedPokemonDescription = descriptionEntry ? descriptionEntry.flavor_text : 'No description available.';
      }).catch((error) => {
        throw new Error(error)
      });
    },
    goToPage(pageNumber) {
  if (pageNumber >= 1 && pageNumber <= this.totalPages) {
    this.currentPage = pageNumber;
  }
},
  },

  computed: {
    FilteredPokemons() {
  if (this.search === '') {
    return this.pokemons.slice((this.currentPage - 1) * 20, this.currentPage * 20);
  } else {
    return this.pokemons.filter((item) => {
      return item.name.includes(this.search);
    });
  }
},
    ShowDialogProp: {
      get() {
        return this.showDialog;
      },
      set(newValue) {
        this.$emit('update:showDialog', newValue); 
      },
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
  overflow: hidden
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
.loading-screen {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(255, 255, 255, 0.8);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  z-index: 9999;
}

.loading-screen p {
  margin-top: 16px;
  font-size: 18px;
}
</style>
