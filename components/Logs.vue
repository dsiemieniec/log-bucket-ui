<template>
  <div>
    <h1>Index: {{ indexName }}</h1>
    <div>Query: {{ query }}</div>
    <table>
      <thead>
        <tr>
          <th></th>
          <th>Field</th>
          <th>Query</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="field in fields">
          <td><input type="checkbox" v-model:value="field.selected"></td>
          <td>{{ field.name }}</td>
          <td><input type="text" v-model:value="field.query" /></td>
        </tr>
      </tbody>
    </table>
    <button v-on:click="search">Search</button>
    <table>
      <thead>
        <tr>
          <th v-for="field in fields">{{ field.name }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="result in results">
          <td v-for="field in fields">{{ field.name in result ? result[field.name].toString() : '' }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: "Logs",
  mounted() {
    this.fetchFields();
  },
  data() {
    return {
      fields: [],
      results: [],
      query: '',
      indexName: 'gettingstarted'
    }
  },
  methods: {
    async fetchFields() {
      const fields = await this.$axios.$get(`/metadata/${this.indexName}/fields`);
      this.fields = fields.map(f => ({selected: false, name: f, query: ''}));
    },

    async search() {
      this.query = this.fields
        .map(f => (f.selected && f.query.length > 0 ? f.name + ':' + f.query : ''))
        .filter(f => f.length > 0)
        .join(' AND ');

      this.results = await this.$axios.$get(`/search/${this.indexName}?q=${this.query}`);
    }
  }
}
</script>

<style scoped>

</style>
