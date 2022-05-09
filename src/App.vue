<template>
  <div id="app">
    <header>
      <nav>
        <div>LOGO</div>
        <form @submit.prevent="searchMovies">
          <input type="text" v-model="inputString" />
          <button type="submit">Search</button>
        </form>
      </nav>
    </header>
    <main>
      <div v-if="filmList.length > 0" class="box">
        <div v-for="(item, index) in filmList" :key="index" class="movie">
          <ul>
            <li>{{ item.title }}</li>
            <li>{{ item.original_title }}</li>
            <li>{{ item.original_language }}</li>
            <li>{{ item.vote_average }}</li>
          </ul>
        </div>
      </div>
      <div v-else>no films</div>
    </main>
  </div>
</template>

<script>
import axios from "axios";
//import SiteHeader from '@/components/HeaderComponent.vue'
//import SiteMain from '@/components/MainComponent.vue'

export default {
  name: "App",
  components: {
    //SiteHeader,
    //SiteMain,
  },
  data() {
    return {
      inputString: "",
      filmList: [],
      apiKey: "8166a56c9ae51f70996c4ce9734e81c3",
      //apiLink: `https://api.themoviedb.org/3/search/movie?api_key=${this.apiKey}&language=en-US&page=1&include_adult=false`,
      //apiLink: `https://api.themoviedb.org/3/search/movie?language=it-IT&page=1&api_key=${this.apiKey}&include_adult=false`,
      apiLink: `https://api.themoviedb.org/3/search/movie?page=1&api_key=8166a56c9ae51f70996c4ce9734e81c3&language=it-IT&include_adult=false`,
    };
  },
  methods: {
    searchMovies() {
      if (this.inputString.replace(/\s+/g, "") !== "") {
        const queryString = this.inputString.replace(" ", "+");

        console.log(`${this.apiLink}&query=${queryString}`);

        axios
          .get(`${this.apiLink}&query=${queryString}`)
          .then((response) => {
            this.filmList = response.data.results;
            //this.loading = false;
            console.log(this.filmList);
          })
          .catch((error) => {
            console.log(`Sorry Error found: ${error}`);
          });

        this.inputString = "";
      } else {
        this.inputString = "";
        console.log("hai inserito una sequenza di spazi");
      }
    },
    /*    searchMovies() {
      const queryString = state.searchInput.replace(" ", "+");

      axios
        .get(`${this.apiLink}&query=${queryString}`)
        .then((response) => {
          this.filmList = response.data.results;
          //this.loading = false;
          console.log(this.filmList);
        })
        .catch((error) => {
          console.log(error);
          error;
          this.error = `Sorry Error found: ${error}`;
        });
    }, */
  },
};
</script>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  //margin-top: 60px;
}

header {
  background-color: black;
  color: white;
}
main {
  background-color: lightgrey;

  .box {
    display: flex;
    flex-wrap: wrap;
    .movie {
      border: 1px solid black;
      padding: 1rem;
      margin: 0.5rem;

      ul {
        list-style: none;
        color: red;
      }
    }
  }
}
</style>
