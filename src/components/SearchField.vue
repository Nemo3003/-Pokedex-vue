<template>
  <v-text-field v-model="localSearch" label="Buscar" placeholder="Charmander" solo @input="updateSearch"></v-text-field>
</template>

<script>
export default {
  props: {
    search: String,
  },
  data() {
    return {
      localSearch: this.search, // Use a local data property
    };
  },
  methods: {
    updateSearch() {
      this.$emit('update-search', this.localSearch); // Emit an event to update the parent's search variable
    },
  },
};
</script>
