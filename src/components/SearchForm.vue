<script>

  function makerequestwiki(data) {
    let API_URL = "https://en.wikipedia.org/w/api.php" + "?origin=*"
    let params = {
      action: "query",
      list: "search",
      format: "json",
      srsearch: data.replace(' ', '%20')
    }
    Object.keys(params).forEach(function(key){API_URL += "&" + key + "=" + params[key];});
    return API_URL
  }

  export default {
    data: () => ({
      words: "",
      results: null,
      searching: false,
    }),
    methods: {
      async fetchData() {
        if(this.words.trim() == '')
          return;
        this.searching = true
        // let request =  makerequestwiki(this.words)
        // let response = await (await fetch(request)).json()
        let API_URL = "http://192.168.1.8:8000/APIv1/" + "searchs/"
        const search = {
          words: this.words,
        }
        let response = await (await fetch(API_URL, { 
          method: "POST",
          body: JSON.stringify(search),
          headers: { "Content-Type": "application/json" } 
        })).json()
        this.results = response?.query.search
        this.searching = false
      }
    }
  }

</script>

<template>
  <div class="container">
    <h1 class="mt-5">Tu Buscador Latino</h1>
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
      <li v-for="result in results" class="list-group-item col-md-8">
        <div class="fw-bold fs-5">{{result?.title}}</div>
        <div class="fw-light">{{result?.snippet.replace(/<[^>]*>?/g, '')}}</div>
        <div class="text-end">
          <span class="badge bg-info text-withe">page id: {{ result?.pageid}}</span>
        </div>
      </li>
    </ul>

  </div>
</template>
