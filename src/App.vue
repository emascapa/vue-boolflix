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
      <font-awesome-icon icon="fa-solid fa-star" /><font-awesome-icon
        icon="fa-solid fa-star"
      />
      <font-awesome-icon icon="fa-regular fa-star" />
      <font-awesome-icon icon="fa-regular fa-star" />
      <div v-if="filmList.length > 0" class="box">
        <h2>MOVIES</h2>
        <div v-for="(item, index) in filmList" :key="index" class="movie">
          <img :src="item.poster_path" :alt="item.original_title" />
          <ul>
            <li>{{ item.title }}</li>
            <li>{{ item.original_title }}</li>
            <li>
              <country-flag
                v-if="item.original_language != null"
                :country="item.original_language"
              />
              <span v-else>ND</span>
            </li>
            <li>{{ item.vote_average }}</li>
            <li>
              <font-awesome-icon
                v-for="i in item.vote_average"
                :key="i"
                icon="fa-solid fa-star"
              />
              <font-awesome-icon
                v-for="n in 5 - item.vote_average"
                :key="n"
                icon="fa-regular fa-star"
              />
            </li>
          </ul>
        </div>
      </div>
      <div v-else>No Movie found</div>

      <div v-if="serieList.length > 0" class="box">
        <h2>SERIES</h2>
        <div v-for="(item, index) in serieList" :key="index" class="movie">
          <img :src="item.poster_path" :alt="item.original_title" />
          <ul>
            <li>{{ item.name }}</li>
            <li>{{ item.original_name }}</li>
            <li>
              <country-flag
                v-if="item.origin_country[0] != null"
                :country="item.origin_country[0]"
              />
              <span v-else>ND</span>
            </li>
            <!-- {{ item.original_language }} -->
            <li>{{ item.vote_average }}</li>
            <li>
              <font-awesome-icon
                v-for="i in item.vote_average"
                :key="i"
                icon="fa-solid fa-star"
              />
              <font-awesome-icon
                v-for="n in 5 - item.vote_average"
                :key="n"
                icon="fa-regular fa-star"
              />
            </li>
          </ul>
        </div>
      </div>
      <div v-else>No Series found</div>
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
      serieList: [],
      apiKey: "8166a56c9ae51f70996c4ce9734e81c3",
      apiMovieLink: `https://api.themoviedb.org/3/search/movie?page=1&api_key=8166a56c9ae51f70996c4ce9734e81c3&language=it-IT&include_adult=false`,
      apiSerieLink: `https://api.themoviedb.org/3/search/tv?page=1&api_key=8166a56c9ae51f70996c4ce9734e81c3&language=it-IT&include_adult=false`,
    };
  },
  methods: {
    searchMovies() {
      if (this.inputString.replace(/\s+/g, "") !== "") {
        let queryString = this.inputString.replace(/\s+/g, " ");
        queryString = queryString.replace(" ", "+");

        console.log(`${this.apiMovieLink}&query=${queryString}`);

        axios
          .get(`${this.apiMovieLink}&query=${queryString}`)
          .then((response) => {
            this.filmList = response.data.results;

            this.makeFlags();

            this.makePosters(this.filmList);

            this.roundVotes(this.filmList);

            //this.makeStars(this.filmList);

            console.log(this.filmList);
          })
          .catch((error) => {
            console.log(`Sorry Error found: ${error}`);
          });

        axios
          .get(`${this.apiSerieLink}&query=${queryString}`)
          .then((response) => {
            this.serieList = response.data.results;

            //this.makeFlags();

            this.makePosters(this.serieList);

            this.roundVotes(this.serieList);

            //this.makeStars(this.serieList);

            console.log(this.serieList);
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
    makeFlags() {
      this.filmList.forEach((item) => {
        if (item.original_language == "en") {
          item.original_language = "us";
        } else if (item.original_language == "ja") {
          item.original_language = "jp";
        } else if (item.original_language == "zh") {
          item.original_language = "cn";
        }
      });
    },
    makePosters(arrayName) {
      arrayName.forEach((item) => {
        if (item.poster_path != null) {
          item.poster_path =
            "https://image.tmdb.org/t/p/w500/" + item.poster_path;
        } else {
          item.poster_path =
            "https://www.tube-culture.com/images/titles_cache/750x1031_movienophoto_2014_1000x1375.jpg";
        }
      });
    },
    roundVotes(arrayName) {
      arrayName.forEach((item) => {
        item.vote_average = Math.round(parseFloat(item.vote_average) * 0.5);
      });
    },
/*     makeStars(arrayName) {
      arrayName.forEach((item) => {
        switch (item.vote_average) {
          case 0:
            item.stars =
              '<font-awesome-icon icon="fa-regular fa-star" /> <font-awesome-icon icon="fa-regular fa-star" /> <font-awesome-icon icon="fa-regular fa-star" /> <font-awesome-icon icon="fa-regular fa-star" /> <font-awesome-icon icon="fa-regular fa-star" />';
            break;
          case 1:
            item.stars =
              '<font-awesome-icon icon="fa-solid fa-star" /> <font-awesome-icon icon="fa-regular fa-star" /> <font-awesome-icon icon="fa-regular fa-star" /> <font-awesome-icon icon="fa-regular fa-star" /> <font-awesome-icon icon="fa-regular fa-star" />';
            break;
          case 2:
            item.stars =
              '<font-awesome-icon icon="fa-solid fa-star" /> <font-awesome-icon icon="fa-solid fa-star" /> <font-awesome-icon icon="fa-regular fa-star" /> <font-awesome-icon icon="fa-regular fa-star" /> <font-awesome-icon icon="fa-regular fa-star" />';
            break;
          case 3:
            item.stars =
              '<font-awesome-icon icon="fa-solid fa-star" /> <font-awesome-icon icon="fa-solid fa-star" /> <font-awesome-icon icon="fa-solid fa-star" /> <font-awesome-icon icon="fa-regular fa-star" /> <font-awesome-icon icon="fa-regular fa-star" />';
            break;
          case 4:
            item.stars =
              '<font-awesome-icon icon="fa-solid fa-star" /> <font-awesome-icon icon="fa-solid fa-star" /> <font-awesome-icon icon="fa-solid fa-star" /> <font-awesome-icon icon="fa-solid fa-star" /> <font-awesome-icon icon="fa-regular fa-star" />';
            break;
          case 5:
            item.stars =
              '<font-awesome-icon icon="fa-solid fa-star" /> <font-awesome-icon icon="fa-solid fa-star" /> <font-awesome-icon icon="fa-solid fa-star" /> <font-awesome-icon icon="fa-solid fa-star" /> <font-awesome-icon icon="fa-solid fa-star" />';
        }
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
