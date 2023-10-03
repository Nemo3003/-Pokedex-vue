<template>
    <v-dialog v-model="dialogVisible" width="800">
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
              <v-chip label class="ml-2">Peso {{ (selectedPokemon.weight * 0.45359237).toFixed(0) }} grs</v-chip>
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
  </template>
  
  <script>
  export default {
    props: {
      showDialog: Boolean,
      selectedPokemon: Object,
      selectedPokemonDescription: String,
    },data() {
    return {
      dialogVisible: this.showDialog, 
    };
  },
  watch: {
    showDialog(newValue) {
      this.dialogVisible = newValue; 
    },
    dialogVisible(newValue) {
      this.$emit('update:showDialog', newValue); 
    },
  },
    methods: {
      capitalize(str) {
        return str.charAt(0).toUpperCase() + str.slice(1);
      },
    },
  };
  </script>
  
