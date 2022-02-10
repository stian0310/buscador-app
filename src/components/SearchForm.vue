<script>
  export default {
    data: () => ({
      words: "",
      results: null,
      searching: false,
    }),
    methods: {
      async fetchData() {
        this.searching = true
        let API_URL = "https://en.wikipedia.org/w/api.php" + "?origin=*"
        let params = {
          action: "query",
          list: "search",
          format: "json",
          srsearch: this.words.replace(' ', '%20')
        }
        Object.keys(params).forEach(function(key){API_URL += "&" + key + "=" + params[key];});
        this.results = await (await fetch(API_URL)).json()     
        this.results = this.results.query.search
        this.searching = false
      }
    }
  }

</script>

<template>
  <div class="container">
    <h1 class="mt-5">Buscador Latino</h1>
    <p class="lead">¿Que desear buscar hoy?</p>
    <form @submit.prevent="fetchData()">
      <div class="input-group mb-3">
        <input v-model="words" type="text" class="form-control">
        <button class="btn btn-primary" type="submit">
          <i class="bi bi-search"></i>
          Buscar
        </button>
      </div>
    </form>

    <div v-if="searching" class="spinner-border text-primary">
      <span class="visually-hidden">Loading...</span>
    </div>

    <ul class="list-group list-group-flush mt-5 mb-5">
      <li v-for="result in results" class="list-group-item">
        <div class="ms-2 me-auto">
          <div class="fw-bold">{{ result?.pageid}} - {{result?.title}}</div>
          {{result?.snippet.replace(/<[^>]*>?/g, '')}}
        </div>
      </li>
    </ul>

  </div>
</template>
