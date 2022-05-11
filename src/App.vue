<template>
  <div id="app">
    <!-- HEADER HERE -->
    <header class="d-flex align-items-center">
      <nav class="container d-flex align-items-center justify-content-between">
        <!-- LOGO -->
        <div class="logo" @click="resetPage">
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
        <!-- SEARCH BAR -->
        <form
          @submit.prevent="searchMovies"
          class="d-flex align-items-center justify-content-end"
        >
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

    <!-- MAIN HERE -->
    <main>
      <div class="container py-4">
        <!-- MOVIES FOUND CONDITION -->
        <div
          v-if="
            filmList.length > 0 && loadingMovies != true && startPage != true
          "
        >
          <h2 class="text-center mb-4">MOVIES FOUND:</h2>

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
                  <li v-show="item.genres && item.genres.length > 0" class="my-2 fs_sm">
                    Genere:&nbsp;
                    <span v-for="(genre, num) in item.genres" :key="genre.id">
                      <span v-if="num != item.genres.length - 1">
                        {{ genre.name }},&nbsp;
                      </span>
                      <span v-else>{{ genre.name }}.</span>
                    </span>
                  </li>
                  <li v-show="item.cast && item.cast.length > 0" class="my-2 fs_sm">
                    Cast:&nbsp;
                    <span v-for="(actor, num) in item.cast" :key="actor.id">
                      <span v-if="num != item.cast.length - 1">
                        {{ actor.name }},&nbsp;
                      </span>
                      <span v-else>{{ actor.name }}.</span>
                    </span>
                  </li>
                  <li class="lead fs_sm mt-2">{{ item.overview }}</li>
                </ul>
              </div>
            </div>
          </div>
        </div>
        <!-- NO MOVIE FOUND CONDITION -->
        <div
          v-else-if="
            filmList.length == 0 && loadingMovies != true && startPage != true
          "
          class="py-4 text-center lead"
        >
          No Movie found
        </div>
        <!-- SEARCHING MOVIES CONDITION -->
        <div
          v-else-if="
            filmList.length == 0 && loadingMovies == true && startPage != true
          "
          class="py-4 text-center lead"
        >
          Searching Movies...
        </div>
        <!-- HOME PAGE MOVIES -->
        <div v-else class="py-4">
          <h2 class="text-center mb-4 text-capitalize">recommended movies</h2>

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
            <div
              v-for="(item, index) in suggestedMovies"
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
                  <li v-show="item.genres && item.genres.length > 0" class="my-2 fs_sm">
                    Genere:&nbsp;
                    <span v-for="(genre, num) in item.genres" :key="genre.id">
                      <span v-if="num != item.genres.length - 1">
                        {{ genre.name }},&nbsp;
                      </span>
                      <span v-else>{{ genre.name }}.</span>
                    </span>
                  </li>
                  <li v-show="item.cast && item.cast.length > 0" class="my-2 fs_sm">
                    Cast:&nbsp;
                    <span v-for="(actor, num) in item.cast" :key="actor.id">
                      <span v-if="num != item.cast.length - 1">
                        {{ actor.name }},&nbsp;
                      </span>
                      <span v-else>{{ actor.name }}.</span>
                    </span>
                  </li>
                  <li class="lead fs_sm mt-2">{{ item.overview }}</li>
                </ul>
              </div>
            </div>
          </div>
        </div>

        <!-- SERIES HERE -->
        <!-- SERIES FOUND CONDITION -->
        <div
          v-if="
            serieList.length > 0 && loadingMovies != true && startPage != true
          "
        >
          <h2 class="text-center mb-4">TV SERIES FOUND:</h2>

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
                  <li v-show="item.genres && item.genres.length > 0" class="my-2 fs_sm">
                    Genere:&nbsp;
                    <span v-for="(genre, num) in item.genres" :key="genre.id">
                      <span v-if="num != item.genres.length - 1">
                        {{ genre.name }},&nbsp;
                      </span>
                      <span v-else>{{ genre.name }}.</span>
                    </span>
                  </li>
                  <li v-show="item.cast && item.cast.length > 0" class="my-2 fs_sm">
                    Cast:
                    <span v-for="(actor2, num2) in item.cast" :key="actor2.id">
                      <span v-if="num2 != item.cast.length - 1">
                        {{ actor2.name }},&nbsp;
                      </span>
                      <span v-else>{{ actor2.name }}.</span>
                    </span>
                  </li>
                  <li class="lead fs_sm mt-2">{{ item.overview }}</li>
                </ul>
              </div>
            </div>
          </div>
        </div>
        <!-- NO SERIE FOUND CONDITION -->
        <div
          v-else-if="
            serieList.length == 0 && loadingMovies != true && startPage != true
          "
          class="py-4 text-center lead"
        >
          No TV Series found
        </div>
        <!-- SEARCHING SERIES CONDITION -->
        <div
          v-else-if="loadingMovies == true && startPage != true"
          class="py-4 text-center lead"
        >
          Searching TV Series...
        </div>
        <!-- SERIES HOMEPAGE -->
        <div v-else class="py-4">
          <h2 class="text-center mb-4 text-capitalize">
            recommended TV Series
          </h2>

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
              v-for="(item, index) in suggestedSeries"
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
                  <li v-show="item.genres && item.genres.length > 0" class="my-2 fs_sm">
                    Genere:&nbsp;
                    <span v-for="(genre, num) in item.genres" :key="genre.id">
                      <span v-if="num != item.genres.length - 1">
                        {{ genre.name }},&nbsp;
                      </span>
                      <span v-else>{{ genre.name }}.</span>
                    </span>
                  </li>
                  <li v-show="item.cast && item.cast.length > 0" class="my-2 fs_sm">
                    Cast:
                    <span v-for="(actor2, num2) in item.cast" :key="actor2.id">
                      <span v-if="num2 != item.cast.length - 1">
                        {{ actor2.name }},&nbsp;
                      </span>
                      <span v-else>{{ actor2.name }}.</span>
                    </span>
                  </li>
                  <li class="lead fs_sm mt-2">{{ item.overview }}</li>
                </ul>
              </div>
            </div>
          </div>
        </div>
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
      suggestedMovies: [
        {
          adult: false,
          backdrop_path: "/nMKdUUepR0i5zn0y1T4CsSB5chy.jpg",
          genre_ids: [18, 28, 80, 53],
          id: 155,
          original_language: "en",
          original_title: "The Dark Knight",
          overview:
            "Il crimine organizzato a Gotham City ha le ore contate. Batman, il tenente Gordon, il nuovo Procuratore Distrettuale e alcuni improbabili epigoni dell'Uomo Pipistrello in imbottiture da hockey hanno dichiarato guerra ai criminali. La loro fortuna e i loro dollari, accumulati in una banca di massima sicurezza, vengono rubati da Joker, un pagliaccio sadico e mascherato che getterà la città nel disordine e nell'anarchia. Riempite le tasche di lame, polvere da sparo e lanugine, Joker sfiderà il cavaliere oscuro di Bruce Wayne e rivelerà il lato oscuro di Harvey Dent, l'eroe procuratore che applica la giustizia e agisce a volto scoperto.",
          popularity: 82.521,
          poster_path: "/qIhsgno1mjbzUbs4H6DaRjhskAR.jpg",
          release_date: "2008-07-14",
          title: "Il cavaliere oscuro",
          video: false,
          vote_average: 8.5,
          vote_count: 27495,
        },
        {
          adult: false,
          backdrop_path: "/hZth9NCeXvvO7Xi98d8q34e1Ier.jpg",
          genre_ids: [16, 10751, 14],
          id: 129,
          original_language: "ja",
          original_title: "千と千尋の神隠し",
          overview:
            "La piccola Chihiro non sopporta l'idea di traslocare e di perdere i propri amici, ma non può far niente per impedirlo. Proprio quando la famiglia è in viaggio verso la nuova casa, il padre imbocca una strada sterrata che termina davanti a un tunnel misterioso. I genitori sceglieranno di attraversarlo nonostante le rimostranze di Chihiro, per giungere a un parco dei divertimenti abbandonato, almeno apparentemente.",
          popularity: 72.784,
          poster_path: "/3PV6lq9BNmoyyDXr5tdNeeESEMn.jpg",
          release_date: "2001-07-20",
          title: "La città incantata",
          video: false,
          vote_average: 8.5,
          vote_count: 12784,
        },
        {
          adult: false,
          backdrop_path: "/fq3wyOs1RHyz2yfzsb4sck7aWRG.jpg",
          genre_ids: [12, 35, 878, 10751],
          id: 105,
          original_language: "en",
          original_title: "Back to the Future",
          overview:
            "Marty McFly è stato catapultato per errore nel 1955, grazie alla macchina del tempo ideata dal suo amico scienziato Doc. Non avendo più \"carburante\" per poter tornare nel futuro si rivolge alla versione più giovane di Doc, che nonostante l'incredulità iniziale si farà in quattro per aiutarlo. Ma nel 1955 non è solo Doc ad essere più giovane, Marty infatti si imbatte casualmente nei suoi genitori, all'epoca teenager, ma l'incontro aggiungerà altri problemi.",
          popularity: 47.558,
          poster_path: "/AkmUoSHkxW9txpzZ52gCcWweEkE.jpg",
          release_date: "1985-07-03",
          title: "Ritorno al futuro",
          video: false,
          vote_average: 8.3,
          vote_count: 16496,
        },
        {
          adult: false,
          backdrop_path: "/o8lMYGHK21tDy2DW6v7QIfTJz4u.jpg",
          genre_ids: [35, 18],
          id: 48254,
          original_language: "it",
          original_title: "Ovosodo",
          overview:
            "La storia di Ovosodo racconta la vita di Piero partendo dalla sua infanzia fino alla paternità. Lo sfondo è la città natale del regista Paolo Virzi, Livorno.",
          popularity: 5.967,
          poster_path: "/s3HeCD1a74Tl9EgSA6UU7O6tpAJ.jpg",
          release_date: "1997-09-12",
          title: "Ovosodo",
          video: false,
          vote_average: 7.2,
          vote_count: 324,
        },
        {
          adult: false,
          backdrop_path: "/8eLXy49T36e0e1YhYvUQOCXNRhm.jpg",
          genre_ids: [14, 18, 9648],
          id: 141,
          original_language: "en",
          original_title: "Donnie Darko",
          overview:
            "Il liceale Donnie Darko, in preda ad un attacco di insonnia, esce dalla casa dei genitori e incontra uno spaventoso coniglio gigante di nome Frank, che gli dice che il mondo finirà tra 28 giorni, 6 ore, 42 minuti e 12 secondi.",
          popularity: 28.13,
          poster_path: "/26hBcVJp8Ix2Bmg7xTj7BYjDZZ1.jpg",
          release_date: "2001-10-24",
          title: "Donnie Darko",
          video: false,
          vote_average: 7.8,
          vote_count: 10371,
        },
      ],
      suggestedSeries: [
        {
          backdrop_path: "/56v2KjBlU4XaOv9rVYEQypROD7P.jpg",
          first_air_date: "2016-07-15",
          genre_ids: [18, 9648, 10765],
          id: 66732,
          name: "Stranger Things",
          origin_country: ["US"],
          original_language: "en",
          original_name: "Stranger Things",
          overview:
            "La scomparsa di un ragazzino in una cittadina porta alla luce un mistero in cui si mescolano esperimenti segreti, spaventose forze soprannaturali e una strana bambina.",
          popularity: 285.001,
          poster_path: "/9URpkzaMMLK5bdJ1a5HBafzYEWj.jpg",
          vote_average: 8.6,
          vote_count: 9779,
        },
        {
          backdrop_path: "/nnmeWeTSQZZEjhj7K2X0rWE9Pef.jpg",
          first_air_date: "2014-08-22",
          genre_ids: [35, 18, 16],
          id: 61222,
          name: "BoJack Horseman",
          origin_country: ["US"],
          original_language: "en",
          original_name: "BoJack Horseman",
          overview:
            "In una Hollywood parallela in cui convivono esseri umani ed animali antropomorfi, l'ex attore di sitcom BoJack Horseman vive nella sua lussuosa villa annegando nell'alcool il grande rimpianto di aver perso la fama di un tempo, quando era protagonista di Horsin' Around. BoJack Horseman ha il grande pregio di dissacrare con originalità l'intero star system attraverso gli occhi di un uomo depresso e disilluso.",
          popularity: 79.994,
          poster_path: "/pB9L0jAnEQLMKgexqCEocEW8TA.jpg",
          vote_average: 8.6,
          vote_count: 1489,
        },
        {
          backdrop_path: "/12NEw3eBUQxK9qdgtegZRsz4Yp8.jpg",
          first_air_date: "2004-09-22",
          genre_ids: [10759, 9648],
          id: 4607,
          name: "Lost",
          origin_country: ["US"],
          original_language: "en",
          original_name: "Lost",
          overview:
            "48 persone sopravvivono ad un tremendo disastro aereo e si ritrovano dispersi su di un'isola dell'Oceano Pacifico che sembra completamente disabitata. Ognuno di loro ha una sua storia, ognuno di loro nasconde dei segreti. Ma tutti quanti, se vogliono continuare a vivere, debbono collaborare, superare le avversità e le divisioni personali e sperare nel soccorso. Che potrebbe non arrivare mai.  La banda di amici, familiari, nemici e stranieri lotta contro l'inclemenza del tempo ed il terreno scosceso. E scopre che anche l'isola nasconde dei segreti, una minaccia incombente, l'urlo di creature misteriose che popolano la giungla e che seminano il panico ed il terrore fra i sopravvissuti.",
          popularity: 163.192,
          poster_path: "/kfD7w4Be9a05SQxVWKaYWfsyg8t.jpg",
          vote_average: 7.9,
          vote_count: 2770,
        },
        {
          backdrop_path: "/rQ5pOJBSqWoRf9evZHkL8rzUD4n.jpg",
          first_air_date: "1989-12-17",
          genre_ids: [10751, 16, 35],
          id: 456,
          name: "I Simpson",
          origin_country: ["US"],
          original_language: "en",
          original_name: "The Simpsons",
          overview:
            "I Simpson (The Simpsons) è una popolare sitcom animata creata dal fumettista statunitense Matt Groening alla fine degli anni ottanta per la Fox Broadcasting Company. È una parodia satirica della società e dello stile di vita statunitensi, personificati dalla famiglia protagonista, di cui fanno parte Homer, Marge e i loro tre figli Bart, Lisa e Maggie.\n\nAmbientato in una cittadina statunitense chiamata Springfield, lo show tratta in chiave umoristica molti aspetti della condizione umana, così come la cultura, la società in generale e la stessa televisione.",
          popularity: 525.533,
          poster_path: "/fxLooktejTUqCvZl9ARhKMFNpbf.jpg",
          vote_average: 8,
          vote_count: 7831,
        },
        {
          backdrop_path: "/8aCek7W6BovH7M4enWjqrGptvQ8.jpg",
          first_air_date: "2013-12-02",
          genre_ids: [16, 35, 10765, 10759],
          id: 60625,
          name: "Rick and Morty",
          origin_country: ["US"],
          original_language: "en",
          original_name: "Rick and Morty",
          overview:
            "Rick è uno scienziato che si è trasferito dalla famiglia di sua figlia Beth, una veterinaria e cardiochirurga per equini. Passa la maggior parte del suo tempo inventando vari gadget high-tech e portando con sé il giovane nipote Morty (e successivamente la nipote Summer) in pericolose e fantastiche avventure attraverso il loro e altri universi paralleli. Questi eventi, aggiunti alla già strana famiglia di Morty, gli causano parecchi disagi sia a scuola che nella vita privata.",
          popularity: 269.189,
          poster_path: "/8kOWDBK6XlPUzckuHDo3wwVRFwt.jpg",
          vote_average: 8.8,
          vote_count: 6337,
        },
      ],
      startPage: true,
      loadingMovies: true,
      //loadingSeries: true,
      apiKey: "8166a56c9ae51f70996c4ce9734e81c3",
      apiMovieLink: `https://api.themoviedb.org/3/search/movie?page=1&api_key=8166a56c9ae51f70996c4ce9734e81c3&language=it-IT&include_adult=false`,
      apiSerieLink: `https://api.themoviedb.org/3/search/tv?page=1&api_key=8166a56c9ae51f70996c4ce9734e81c3&language=it-IT&include_adult=false`,
    };
  },

  mounted() {
    //assemblo i film consigliati
    this.makeFlags(this.suggestedMovies);

    this.makePosters(this.suggestedMovies);

    this.roundVotes(this.suggestedMovies);

    this.getCast(this.suggestedMovies, "movie");

    this.getGenres(this.suggestedMovies, "movie");

    //assemblo le serie consigliate
    this.makePosters(this.suggestedSeries);

    this.roundVotes(this.suggestedSeries);

    this.getCast(this.suggestedSeries, "tv");

    this.getGenres(this.suggestedSeries, "tv");
  },

  methods: {
    searchMovies() {
      //se la stringa input non è una sequenza di spazi si va avanti
      if (this.inputString.replace(/\s+/g, "") !== "") {
        let queryString = this.inputString.replace(/\s+/g, " ");

        queryString = queryString.replace(" ", "+");

        //console.log(`${this.apiMovieLink}&query=${queryString}`);

        //condizione pagina iniziale -> false
        if (this.startPage == true) {
          this.startPage = false;
        }

        //condizione per il caricamento
        if (this.loadingMovies == false) {
          this.loadingMovies = true;
        }

        //chiamata http per i film
        axios
          .get(`${this.apiMovieLink}&query=${queryString}`)
          .then((response) => {

            //array risultati
            this.filmList = response.data.results;

            //metto a posto le key per visualizzare l'oggetto in pagina
            this.makeFlags(this.filmList);

            this.makePosters(this.filmList);

            this.roundVotes(this.filmList);

            this.getCast(this.filmList, "movie");

            this.getGenres(this.filmList, "movie");

            console.log(`Movies found for ${queryString}:`, this.filmList);

            //console.log('ultimo log',this.filmList[0].cast);
          })
          .catch((error) => {
            console.log(`Sorry Error found: ${error}`);
          });

        //chiamata http per le serie
        axios
          .get(`${this.apiSerieLink}&query=${queryString}`)
          .then((response) => {
            this.serieList = response.data.results;

            //this.makeFlags();

            this.makePosters(this.serieList);

            this.roundVotes(this.serieList);

            this.getCast(this.serieList, "tv");

            this.getGenres(this.serieList, "tv");

            console.log(`Series found for ${queryString}:`, this.serieList);
          })
          .catch((error) => {
            console.log(`Sorry Error found: ${error}`);
          });

        //azzero la search bar
        this.inputString = "";

        //caricamento ok
        this.loadingMovies = false;

      } else {

        this.inputString = "";
        console.log("hai inserito una sequenza di spazi");

      }

    },

    makeFlags(arrayName) {
      //cambio il codice di alcuni paesi per visualizzare le bandierine
      arrayName.forEach((item) => {
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
      //modifico la key dell'oggetto movie/serie per risalire all'url della locandina
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
      //funzione per ottenere il cast di un film o serie
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

      //console.log(`array cast:`, arrayName);
    },

    getGenres(arrayName, movieOrTv) {
      //funzione per ottenere i generi di un film
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

    resetPage() {
      //funzione per resettare la pagina
      this.filmList = [];
      this.serieList = [];

      this.loadingMovies = true;

      this.startPage = true;
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

  user-select: none;

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

      user-select: none;
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
