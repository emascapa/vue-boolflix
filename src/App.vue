<template>
  <div id="app">
    <header class="d-flex align-items-center">
      <nav class="container d-flex align-items-center justify-content-between">
        <div class="logo">
          <img
            src="@/assets/img/logo-sm.png"
            alt="logo"
            class="img-fluid d-block d-md-none"
          />
          <img
            src="@/assets/img/logo.png"
            alt="logo"
            class="img-fluid d-none d-md-block"
          />
        </div>
        <form @submit.prevent="searchMovies" class="d-flex align-items-center">
          <input
            type="text"
            v-model="inputString"
            class="form-control mx-2"
            placeholder="Search for Movies or TV Series"
          />
          <button type="submit" class="btn btn-secondary">Search</button>
        </form>
      </nav>
    </header>
    <main>
      <div class="container py-4">
        <div v-if="filmList.length > 0">
          <h2 class="text-center mb-4">MOVIES</h2>

          <div
            class="
              row
              row-cols-1
              row-cols-sm-2
              row-cols-md-3
              row-cols-lg-4
              row-cols-xl-5
            "
          >
            <div v-for="(item, index) in filmList" :key="index" class="col p-2">
              <div class="movie_wrapper rounded rounded-4">
                <img
                  :src="item.poster_path"
                  :alt="item.original_title"
                  class="img-fluid"
                />
                <ul class="movie_info list-unstyled p-3">
                  <li class="lead mb-1 mb-1">Titolo: {{ item.title }}</li>
                  <li>Titolo originale: {{ item.original_title }}</li>
                  <li>
                    <country-flag
                      v-if="item.original_language != null"
                      :country="item.original_language"
                    />
                    <span v-else>Paese: ND</span>
                  </li>
                  <li>
                    Voto:
                    <font-awesome-icon
                      v-for="i in item.vote_average"
                      :key="i"
                      icon="fa-solid fa-star"
                      class="text-warning"
                    />
                    <font-awesome-icon
                      v-for="n in 5 - item.vote_average"
                      :key="n + item.vote_average"
                      icon="fa-regular fa-star"
                      class="text-warning"
                    />
                    <span class="fs_sm">&nbsp; ({{ item.vote_count }})</span>
                  </li>
                  <li v-show="item.genres.length > 0" class="my-2 fs_sm">
                    Genere:&nbsp;
                    <span v-for="(genre, num) in item.genres" :key="genre.id">
                      <span v-if="num != item.genres.length - 1">
                        {{ genre.name }},&nbsp;
                      </span>
                      <span v-else>{{ genre.name }}.</span>
                    </span>
                  </li>
                  <li v-show="item.cast.length > 0" class="my-2 fs_sm">
                    Cast:&nbsp;
                    <span v-for="(actor, num) in item.cast" :key="actor.id">
                      <span v-if="num != item.cast.length - 1">
                        {{ actor.name }},&nbsp;
                      </span>
                      <span v-else>{{ actor.name }}.</span>
                    </span>
                  </li>
                  <li class="lead fs_sm">{{ item.overview }}</li>
                </ul>
              </div>
            </div>
          </div>
        </div>
        <div v-else class="py-4 text-center lead">No Movie found</div>

        <div v-if="serieList.length > 0">
          <h2 class="text-center mb-4">TV SERIES</h2>

          <div
            class="
              row
              row-cols-1
              row-cols-sm-2
              row-cols-md-3
              row-cols-lg-4
              row-cols-xl-5
              mb-4
            "
          >
            <div
              v-for="(item, index) in serieList"
              :key="index"
              class="col p-2"
            >
              <div class="movie_wrapper rounded rounded-4">
                <img
                  :src="item.poster_path"
                  :alt="item.original_title"
                  class="img-fluid"
                />
                <ul class="movie_info list-unstyled p-3">
                  <li class="lead mb-1">Titolo: {{ item.name }}</li>
                  <li>Titolo originale: {{ item.original_name }}</li>
                  <li>
                    <country-flag
                      v-if="item.origin_country[0] != null"
                      :country="item.origin_country[0]"
                    />
                    <span v-else>Paese: ND</span>
                  </li>
                  <li>
                    Voto:
                    <font-awesome-icon
                      v-for="i in item.vote_average"
                      :key="i"
                      icon="fa-solid fa-star"
                      class="text-warning"
                    />
                    <font-awesome-icon
                      v-for="n in 5 - item.vote_average"
                      :key="n + item.vote_average"
                      icon="fa-regular fa-star"
                      class="text-warning"
                    />
                    <span class="fs_sm">&nbsp; ({{ item.vote_count }})</span>
                  </li>
                  <li v-show="item.genres.length > 0" class="my-2 fs_sm">
                    Genere:&nbsp;
                    <span v-for="(genre, num) in item.genres" :key="genre.id">
                      <span v-if="num != item.genres.length - 1">
                        {{ genre.name }},&nbsp;
                      </span>
                      <span v-else>{{ genre.name }}.</span>
                    </span>
                  </li>
                  <li v-show="item.cast.length > 0" class="my-2 fs_sm">
                    Cast:
                    <span v-for="(actor2, num2) in item.cast" :key="actor2.id">
                      <span v-if="num2 != item.cast.length - 1">
                        {{ actor2.name }},&nbsp;
                      </span>
                      <span v-else>{{ actor2.name }}.</span>
                    </span>
                  </li>
                  <li class="lead fs_sm">{{ item.overview }}</li>
                </ul>
              </div>
            </div>
          </div>
        </div>
        <div v-else class="py-4 text-center lead">No Series found</div>
      </div>
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
      suggestedMovies: [],
      suggestedSeries: [],
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

            this.getCast(this.filmList, "movie");

            this.getGenres(this.filmList, "movie");

            console.log("Movies found:", this.filmList);

            //console.log('ultimo log',this.filmList[0].cast);
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

            this.getCast(this.serieList, "tv");

            this.getGenres(this.serieList, "tv");

            console.log("Series found:", this.serieList);
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
    getCast(arrayName, movieOrTv) {
      arrayName.forEach((item) => {
        axios
          .get(
            `https://api.themoviedb.org/3/${movieOrTv}/${item.id}/credits?api_key=${this.apiKey}&language=it-IT`
          )
          .then((response) => {
            //console.log('api cast response:', response);
            //item.cast = response.data.cast;
            if (response.data.cast.length <= 5) {
              this.$set(item, "cast", response.data.cast);
            } else {
              this.$set(item, "cast", response.data.cast.slice(0, 5));
            }
          })
          .catch((error) => {
            console.log(`Sorry Error found getting the cast: ${error}`);
          });

        //console.log(`array cast ${index}:`, item.cast);
      });

      console.log(`array cast:`, arrayName);
    },
    getGenres(arrayName, movieOrTv) {
      arrayName.forEach((item) => {
        axios
          .get(
            `https://api.themoviedb.org/3/${movieOrTv}/${item.id}?api_key=${this.apiKey}&language=it-IT`
          )
          .then((response) => {
            //console.log('api GENRE response:', response);
            //item.cast = response.data.cast;
            if (response.data.genres.length <= 5) {
              this.$set(item, "genres", response.data.genres);
            } else {
              this.$set(item, "genres", response.data.genres.slice(0, 5));
            }
          })
          .catch((error) => {
            console.log(`Sorry Error found getting the cast: ${error}`);
          });

        //console.log(`array cast ${index}:`, item.cast);
      });

      //console.log(`array cast:`, arrayName);
    },
  },
};
</script>

<style lang="scss">
@import "@/assets/scss/style.scss";

header {
  background-color: $bg_header;
  color: white;

  height: 75px;

  .logo > img {
    height: 40px;
    cursor: pointer;
  }
}
main {
  background-color: $bg_main;

  color: $light_theme;

  min-height: calc(100vh - 75px);

  .row {
    margin-bottom: 2rem;
  }

  .movie_wrapper {
    position: relative;

    height: 100%;

    overflow: hidden;

    &:hover .movie_info {
      display: block;
      z-index: 10;
    }

    img {
      object-fit: cover;
      height: 100%;
    }

    .movie_info {
      display: none;

      position: absolute;
      top: 0;
      left: 0;

      height: 100%;
      width: 100%;

      background-color: $card_theme;

      opacity: 0.95;

      overflow-y: scroll;

      //text-overflow: ellipsis;
    }
  }
}
</style>
