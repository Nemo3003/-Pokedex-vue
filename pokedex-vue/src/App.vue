<template>
  <div>
    <!-- Check if a Pokemon is selected -->
    <div v-if="selectedPokemon">
      <h1>{{ selectedPokemon.name }}</h1>
      <div class="pokemon-details">
        <img :src="selectedPokemon.imageUrl" :alt="selectedPokemon.name" />
        <div class="basic-info">
          <h2>Basic Information</h2>
          <p>Type: {{ selectedPokemon.type }}</p>
          <p>Weight: {{ selectedPokemon.weight }} kg</p>
        </div>
        <div class="description">
          <h2>Description (in Spanish)</h2>
          <p>{{ selectedPokemon.description }}</p>
        </div>
        <div class="moves">
          <h2>Moves</h2>
          <ul>
            <li v-for="move in selectedPokemon.moves" :key="move">{{ move }}</li>
          </ul>
        </div>
      </div>
      <button @click="goBack">Back to Pokemon List</button>
    </div>

    <!-- Display the list of Pokemon when no Pokemon is selected -->
    <div v-else>
      <h1>Pokemon List</h1>
      <ul>
        <li v-for="pokemon in pokemonList" :key="pokemon.name">
          <div class="pokemon-card">
            <img :src="pokemon.imageUrl" :alt="pokemon.name" />
            <div class="pokemon-info">
              <h2>{{ pokemon.name }}</h2>
              <p>Type: {{ pokemon.type }}</p>
              <p>Weight: {{ pokemon.weight }} kg</p>
            </div>
            <button @click="showPokemonDetails(pokemon)">Show Details</button>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      pokemonList: [], // Array to store the fetched Pokemon data
      selectedPokemon: null, // Currently selected Pokemon
    };
  },
  created() {
    // Fetch data when the component is created
    this.fetchPokemonList();
  },
  methods: {
    async fetchPokemonList() {
      try {
        const response = await axios.get("https://pokeapi.co/api/v2/pokemon/");
        const pokemonData = response.data.results;
        for (const pokemon of pokemonData) {
          // Fetch additional details for each Pokemon
          const pokemonDetails = await this.fetchPokemonDetails(pokemon.url);
          this.pokemonList.push({
            name: pokemon.name,
            imageUrl: pokemonDetails.sprites.front_default,
            type: pokemonDetails.types[0].type.name,
            weight: pokemonDetails.weight / 10, // Convert to kg
            description: "Placeholder Description", // You need to fetch the description separately
            moves: pokemonDetails.moves.map((move) => move.move.name),
          });
        }
      } catch (error) {
        console.error("Error fetching Pokemon data:", error);
      }
    },
    async fetchPokemonDetails(url) {
      try {
        const response = await axios.get(url);
        const pokemonDetails = response.data;

        // Fetch the description from the species URL
        const speciesResponse = await axios.get(pokemonDetails.species.url);
        const speciesData = speciesResponse.data;

        // Find the description in the species data
        const descriptionEntry = speciesData.flavor_text_entries.find(
          (entry) => entry.language.name === "es" // Language code for Spanish
        );

        const description = descriptionEntry ? descriptionEntry.flavor_text : "No description available.";

        this.selectedPokemon = {
          name: pokemonDetails.name,
          imageUrl: pokemonDetails.sprites.front_default,
          type: pokemonDetails.types[0].type.name,
          weight: pokemonDetails.weight / 10, 
          description: description, 
          moves: pokemonDetails.moves.map((move) => move.move.name),
        };
      } catch (error) {
        console.error("Error fetching Pokemon details:", error);
      }
    },
    goBack() {
      // Clear the selected Pokemon and go back to the list
      this.selectedPokemon = null;
    },
  },
};
</script>

<style scoped>
/* Add your CSS styles here */
.pokemon-card {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
}

img {
  max-width: 100px;
  max-height: 100px;
  margin-right: 20px;
}

button {
  margin-left: 20px;
}
</style>
