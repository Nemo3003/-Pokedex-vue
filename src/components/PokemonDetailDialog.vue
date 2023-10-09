<template>
    <v-dialog v-model="dialogVisible" width="800">
      <div class="dialog-container">
      <div class="font-size-container">
        <p class="font-size-label">Disminuir/Aumentar fuente</p>
        <div class="font-size-buttons">
          <v-btn @click="DecreaseFontSize">-</v-btn>
          <v-btn @click="IncreaseFontSize">+</v-btn>
        </div>
      </div>
    </div>
      <v-card v-if="selectedPokemon" class="px-4">
        <v-btn icon @click="CloseDialog" class="close-button">
        <v-icon>mdi-close</v-icon>
      </v-btn>
        <v-container class="px-2 text-center">
          <v-row class="d-flex align-center">
            <v-col cols="4">
              <img :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${selectedPokemon.id}.png`" :alt="selectedPokemon.name" width="80%" />
            </v-col>
            <v-col cols="8">
              <h1 :style="{ fontSize: fontSize + 'px' }">{{ Capitalize(selectedPokemon.name) }}</h1>
              <v-chip label v-for="type in selectedPokemon.types" :key="type.slot" class="mr-2"
              :style="{ fontSize: fontSize + 'px' }"
              >{{ type.type.name }}</v-chip>
              <v-divider class="my-4"></v-divider>
              <v-chip label :style="{ fontSize: fontSize + 'px' }" class="ml-2">Altura {{ selectedPokemon.height * 2.54 }} cm</v-chip>
              <v-chip label class="ml-2" :style="{ fontSize: fontSize + 'px' }">Peso {{ (selectedPokemon.weight /10).toFixed(0) }} kg</v-chip>
              <v-divider class="my-4"></v-divider>
              <h3>Descripcion</h3>
              <p :style="{ fontSize: fontSize + 'px' }">{{ selectedPokemonDescription }}</p>
              <v-divider class="my-4"></v-divider>
            </v-col>
          </v-row>
          <h2 :style="{ fontSize: fontSize + 'px' }">Moves</h2>
          <v-table>
            <template>
              <tbody v-for="item in selectedPokemon.moves" :key="item.move.name">
                <tr>
                  <td :style="{ fontSize: fontSize + 'px' }">{{ item.move.name }}</td>
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
  },
  data() {
    return {
      dialogVisible: this.showDialog,
      fontSize: 16,
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
    Capitalize(str) {
      return str.charAt(0).toUpperCase() + str.slice(1);
    },
    CloseDialog() {
      this.dialogVisible = false; 
    },
    IncreaseFontSize() {
    if (this.fontSize < 30) { 
      this.fontSize += 2;
    }
  },

  DecreaseFontSize() {
    if (this.fontSize > 10) { 
      this.fontSize -= 2; 
    }
  },
  },
};
</script>
<style>
:root {
  --primary-color: #007bff;
  --primary-color-hover: #0056b3;
  --primary-color-active: #00469e;
  --secondary-color: #f44336;
  --secondary-color-hover: #d32f2f;
  --background-opacity: 0.9;
  --border-radius: 5px;
  --button-size: 36px;
}

.dialog-container {
  display: flex;
  position: relative;
}

.font-size-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 16px;
  text-align: center;
}

.font-size-label {
  font-size: 16px;
  margin-bottom: 8px;
  color: #333;
  background-color: rgba(255, 255, 255, var(--background-opacity));
  padding: 8px 16px;
  border-radius: var(--border-radius);
}

.font-size-buttons {
  display: flex;
  justify-content: center;
  align-items: center;
}

.font-size-buttons v-btn {
  margin: 0 8px;
  color: white;
  background-color: var(--primary-color);
  border-radius: 50%;
  width: var(--button-size);
  height: var(--button-size);
  font-size: 20px;
  cursor: pointer;
}

/* Add hover and active styles for buttons */
.font-size-buttons v-btn:hover {
  background-color: var(--primary-color-hover);
}

.font-size-buttons v-btn:active {
  background-color: var(--primary-color-active);
}

.close-button {
  position: absolute;
  top: 10px;
  right: 10px;
  color: #fff;
  background-color: var(--secondary-color);
  border-radius: 50%;
  width: var(--button-size);
  height: var(--button-size);
  font-size: 20px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.close-button:hover {
  background-color: var(--secondary-color-hover);
}

.close-button:focus {
  outline: none;
}
</style>
