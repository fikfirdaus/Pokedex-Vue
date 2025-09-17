<script>
export default {
  data() {
    return {
      pokemonList: [],
      viewedAsDetail: false,
      selectedPokemon: '',
      search: '',
      get1Pokemon: [],
      suggestedPokemon: ''
    }
  },
  methods: {
    async getListPokemon() {
      let response = await fetch(`https://pokeapi.co/api/v2/pokemon`);
      let data = await response.json();
      let results = data.results;

      results.forEach(item => {
        this.getEachPokemon(item)
      });
    },
    async getEachPokemon(result) {
      let response = await fetch("https://pokeapi.co/api/v2/pokemon/" + result.name);
      let data = await response.json();

      // console.log(data);
      this.pokemonList.push(data);
      this.pokemonList.sort((a, b) => a.id - b.id)
    },
    async viewDetailPokemon(pokemonName) {
      this.viewedAsDetail = true;
      let response = await fetch("https://pokeapi.co/api/v2/pokemon/" + pokemonName);
      let data = await response.json();

      // console.log(data)
      this.selectedPokemon = data;
    },
    async getGen1Pokemon(){
      let response = await fetch(`https://pokeapi.co/api/v2/pokemon?limit=151`);
      let data = await response.json();
      let result = data.results
      this.get1Pokemon = result
      console.log(this.get1Pokemon)
    }
  },
  watch: {
    search() {
      let filteredPokemon = this.get1Pokemon.filter(item => {
          return item.name.includes(this.search)
      })

      this.suggestedPokemon = filteredPokemon.slice(0, 4);
    }
  },
  mounted() {
    this.getListPokemon();
    this.getGen1Pokemon();
  }
}
</script>

<template>
  <div class="container-flud bg-dark bg-gradient">
    <div class="container bg-dark" style="min-height: 100vh;">
      <div class="row justify-content-center">
        <div class="col-5">
          <img src="/src/assets/pngwing.com.png" alt="" width="100%;">
        </div>
      </div>

      <div class="row justify-content-center mt-4 text-white">
        <div class="col-6">
          <input type="text" class="form-control" placeholder="search pokemon" v-model="search">
          <div>
            <label class="mt-2" for="">Suggestion : &nbsp;</label><span class="suggested-pokemon" v-for="i in suggestedPokemon" @click="viewDetailPokemon(i.name)">{{i.name}} | </span>
          </div>
        </div>
      </div>

      <div class="main mt-5 pt-5">
        <div class="view-all text-white" v-if="!viewedAsDetail">
          <div class="row px-5">
            <div class="col-12 col-md-4 col-lg-3 mb-3" v-for="item in pokemonList"
              @click="viewDetailPokemon(item.name)">
              <div class="card text-center text-white">
                <img :src="item.sprites.front_default">
                <label style="font-size: 17px; padding-bottom: 5px;" for="">{{ item.name }}</label>
              </div>
            </div>
          </div>
        </div>

        <div class="view-detail text-white" v-else>
          <div>
            <button class="btn btn-secondary" @click="viewedAsDetail = false">View All Pokemon</button>
          </div>

          <div class="row">
            <div class="col-12 col-md-6 mt-3">
              <div class="card">
                <img :src="selectedPokemon.sprites.front_default">
              </div>
            </div>
            <div class="col-12 col-md-6 mt-3">
              <h2>{{ selectedPokemon.name }}</h2>
              <p>
                <label for="">Type : </label> <br />
                <span v-for="i in selectedPokemon.types">
                  {{ i.type.name }} |
                </span>
              </p>
              <p>
                <label for="">Skills : </label>
                <span v-for="i in selectedPokemon.moves">
                  {{ i.move.name }} |
                </span>
              </p>
            </div>
          </div>

        </div>
      </div>

    </div>
  </div>
</template>

<style scoped>
.card {
  background: rgba(255, 255, 255, 0.21);
  border-radius: 10px;
  box-shadow: 0 4px 30px rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(9px);
  border-left: 2px solid rgba(255, 255, 255, 0.58);
  border-top: 2px solid rgba(255, 255, 255, 0.58);
}

.suggested-pokemon {
  cursor: pointer;
}
</style>
