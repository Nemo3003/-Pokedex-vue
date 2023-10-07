<template>
    <v-dialog v-model="dialogVisible" width="800">
      <div class="dialog-container">
      <div class="font-size-container">
        <p class="font-size-label">Disminuir/Aumentar fuente</p>
        <div class="font-size-buttons">
          <v-btn @click="decreaseFontSize">-</v-btn>
          <v-btn @click="increaseFontSize">+</v-btn>
        </div>
      </div>
    </div>
      <v-card v-if="selectedPokemon" class="px-4">
        <v-btn icon @click="closeDialog" class="close-button">
        <v-icon>mdi-close</v-icon>
      </v-btn>
        <v-container class="px-2 text-center">
          <v-row class="d-flex align-center">
            <v-col cols="4">
              <img :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${selectedPokemon.id}.png`" :alt="selectedPokemon.name" width="80%" />
            </v-col>
            <v-col cols="8">
              <h1 :style="{ fontSize: fontSize + 'px' }">{{ capitalize(selectedPokemon.name) }}</h1>
              <v-chip label v-for="type in selectedPokemon.types" :key="type.slot" class="mr-2"
              :style="{ fontSize: fontSize + 'px' }"
              >{{ type.type.name }}</v-chip>
              <v-divider class="my-4"></v-divider>
              <v-chip label :style="{ fontSize: fontSize + 'px' }" class="mb-2">Altura {{ selectedPokemon.height * 2.54 }} cm</v-chip>
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
    capitalize(str) {
      return str.charAt(0).toUpperCase() + str.slice(1);
    },
    closeDialog() {
      this.dialogVisible = false; 
    },
    increaseFontSize() {
    if (this.fontSize < 30) { 
      this.fontSize += 2;
    }
  },

  decreaseFontSize() {
    if (this.fontSize > 10) { 
      this.fontSize -= 2; 
    }
  },
  },
};
</script>
<style scoped>
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
  color: #333; /* Label text color */
  background-color: rgba(255, 255, 255, 0.9); /* Label background color with some transparency */
  padding: 8px 16px; /* Adjust padding for better visibility */
  border-radius: 5px; /* Rounded label */
}

.font-size-buttons {
  display: flex;
  justify-content: center;
  align-items: center;
}

.font-size-buttons v-btn {
  margin: 0 8px; /* Add horizontal margin */
  color: white;
  background-color: #007bff; /* Button background color */
  border-radius: 50%; /* Rounded button */
  width: 36px; /* Adjust button width as needed */
  height: 36px; /* Adjust button height as needed */
  font-size: 20px; /* Adjust button font size as needed */
}

/* Add hover and active styles for buttons */
.font-size-buttons v-btn:hover {
  background-color: #0056b3; /* Hover background color */
}

.font-size-buttons v-btn:active {
  background-color: #00469e; /* Active background color */
}
.close-button {
  position: absolute; /* Position the button absolutely within the container */
  top: 10px; /* Adjust top position as needed */
  right: 10px; /* Adjust right position as needed */
  color: #fff; /* Button text color */
  background-color: #f44336; /* Button background color */
  border-radius: 50%; /* Rounded button */
  width: 36px; /* Adjust button width as needed */
  height: 36px; /* Adjust button height as needed */
  font-size: 20px; /* Adjust button font size as needed */
  cursor: pointer; /* Change cursor on hover */
  transition: background-color 0.3s ease; /* Add transition for button background color */
}

.close-button:hover {
  background-color: #d32f2f; /* Hover background color */
}

.close-button:focus {
  outline: none; /* Remove focus outline */
}
</style>
