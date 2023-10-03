<template>
    <div>
      <!-- Iterate over row groups -->
      <v-row v-for="(rowGroup, index) in rowGroups" :key="index">
        <!-- Iterate over Pokémon cards in each row -->
        <v-col cols="12" sm="6" md="4" lg="3" v-for="pokemon in rowGroup" :key="pokemon.name">
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
                  <p class="text-caption text-center">Weight: {{ pokemon.weight }} kg</p>
                </v-col>
              </v-row>
            </v-container>
          </v-card>
        </v-col>
      </v-row>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      filteredPokemons: Array,
    },
    computed: {
      rowGroups() {
        // Create row groups with a maximum of 4 columns per row
        const groups = [];
        let currentGroup = [];
        for (const pokemon of this.filteredPokemons) {
          if (currentGroup.length === 4) {
            groups.push(currentGroup);
            currentGroup = [];
          }
          currentGroup.push(pokemon);
        }
        if (currentGroup.length > 0) {
          groups.push(currentGroup);
        }
        return groups;
      },
    },
    methods: {
      showPokemon(id) {
        this.$emit('show-pokemon', id);
      },
    },
  };
  </script>
  