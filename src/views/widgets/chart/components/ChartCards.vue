<script setup lang="ts">
import { ref, computed } from 'vue';
import SvgSprite from '@/components/shared/SvgSprite.vue';
import { useTheme } from 'vuetify';

const show = ref(false);
let movieArray = ref([]);
let seriesArray = ref([]);
let ratedArray = ref([]);
let popularArray = ref([]);
let southArray = ref([]);
let savedMovies = ref([]);


const options = {
  method: 'GET',
  headers: {
    accept: 'application/json',
    Authorization:
      'Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJkZDRiZTYzZTNkNWRhYzI5MWMwYzg5Y2QzZWEzY2ZlOCIsInN1YiI6IjY2NDQwZWJhNDRhMDkxOGFkZTNhOWEzYyIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.AqL2l1ne8pkNdrSiz1aPGKLIx-ZFjgwvhYUx-3ybk6w'
  }
};

const fetchGenre = async () => {
  try {
    const response = await fetch('https://api.themoviedb.org/3/genre/tv/list?language=en', options);
    if (!response.ok) {
      throw new Error('Network response was not ok');
    }
    const data = await response.json();
    seriesArray.value = data.genres;
    console.log('SERIES');
    console.log(seriesArray.value);
  } catch (error) {
    console.error('Fetch error:', error);
  }
};

const fetchMovies = async () => {
  try {
    const response = await fetch(
      'https://api.themoviedb.org/3/discover/movie?include_adult=false&include_video=false&language=en-US&page=1&sort_by=popularity.desc',
      options
    );
    if (!response.ok) {
      throw new Error('Network response was not ok');
    }
    const data = await response.json();
    movieArray.value = data.results;
    console.log('MOVIES');
    console.log(movieArray.value);
  } catch (error) {
    console.error('Fetch error:', error);
  }
};

const fetchRatedMovies = async () => {
  try {
    const response = await fetch('https://api.themoviedb.org/3/movie/top_rated?language=en-US&page=1', options);
    if (!response.ok) {
      throw new Error('Network response was not ok');
    }
    const data = await response.json();
    ratedArray.value = data.results;
    console.log('RATED MOVIES');
    console.log(ratedArray.value);
  } catch (error) {
    console.error('Fetch error:', error);
  }
};

const fetchSeries = async () => {
  try {
    const response = await fetch(
      'https://api.themoviedb.org/3/discover/tv?language=en-US&sort_by=popularity.desc&page=1',
      options
    );
    if (!response.ok) {
      throw new Error('Network response was not ok');
    }
    const data = await response.json();
    popularArray.value = data.results;
    console.log('Series');
    console.log(popularArray.value);
  } catch (error) {
    console.error('Fetch error:', error);
  }
};

const fetchSouthparkEpisode = async (episode: number) => {
  try {
    const response = await fetch(`https://spapi.dev/api/episodes/${episode}`);
    if (!response.ok) {
      throw new Error('Network response was not ok');
    }
    const data = await response.json();
    southArray.value = [data.data]; // Asegúrate de que data.data es un array o conviértelo en uno
    console.log('SOUTH PARK');
    console.log(southArray.value);
  } catch (error) {
    console.error('Fetch error:', error);
  }
};

const saveMovie = (movie) => {
  if (!savedMovies.value.includes(movie)) {
    savedMovies.value.push(movie);
    console.log('Movie saved:', movie);
  }
};

function randomIntFromInterval(min: number, max: number) { // min and max included 
  return Math.floor(Math.random() * (max - min + 1) + min);
}

const rndInt = randomIntFromInterval(1, 314);
console.log(rndInt);

fetchMovies();
fetchGenre();
fetchRatedMovies();
fetchSeries();
fetchSouthparkEpisode(rndInt);
</script>

<template>
  <div>
    <v-row>
      <v-col>
        <h1>Generos</h1>
      </v-col>
    </v-row>
    <v-row>
      <v-col v-for="genre in seriesArray" :key="genre.id" cols="1" md="1" lg="1">
        <v-chip variant="elevated">
          {{ genre.name }}
        </v-chip>
      </v-col>
    </v-row>
  </div>
  <div>
    <v-row>
      <v-col>
        <h1>SOUTH PARK RANDOM EPISODE</h1>
      </v-col>
    </v-row>
    <v-row>
      <v-col v-for="south in southArray" :key="south.id" cols="12" md="6" lg="2">
        <v-card class="mx-auto" max-width="344">
          <v-img height="300px" :src="south.thumbnail_url" cover></v-img>
          <v-card-title>{{ south.name }}</v-card-title>
          <v-card-subtitle>{{ south.air_date }}</v-card-subtitle>
          <v-card-text>Temporada: {{ south.season }} - Episodio: {{ south.episode }}</v-card-text>
          <v-card-actions>
            <v-btn color="orange-lighten-2" text>Explore</v-btn>
            <v-spacer></v-spacer>
            <v-btn :icon="show ? 'mdi-chevron-up' : 'mdi-chevron-down'" @click="show = !show"></v-btn>
          </v-card-actions>
          <v-expand-transition>
            <div v-show="show">
              <v-divider></v-divider>
              <v-card-text>{{ south.description }}</v-card-text>
            </div>
          </v-expand-transition>
        </v-card>
        
      </v-col>
    </v-row>

  </div>

  <!-- PELICULAS  -->
  <div>
    <v-row>
      <v-col>
        <h1>Películas</h1>
      </v-col>
    </v-row>
  </div>
  <!--Movies-->
  <v-row class="mb-0">
    <v-col v-for="movie in movieArray" :key="movie.id" cols="12" md="12" lg="2">
      <v-card class="mx-auto" max-width="344">
        <v-img height="200px" :src="'https://image.tmdb.org/t/p/w500/' + movie.poster_path" cover></v-img>
        <v-card-title>{{ movie.title }}</v-card-title>
        <v-card-subtitle>{{ movie.release_date }}</v-card-subtitle>
        <v-card-actions>
          <v-btn color="orange-lighten-2" text>Explore</v-btn>
          <v-spacer></v-spacer>
          <v-btn :icon="show ? 'mdi-chevron-up' : 'mdi-chevron-down'" @click="show = !show"></v-btn>
        </v-card-actions>
        <v-expand-transition>
          <div v-show="show">
            <v-divider></v-divider>
            <v-card-text>{{ movie.overview }}</v-card-text>
          </div>
        </v-expand-transition>
      </v-card>
    </v-col>
  </v-row>
  <!-- Top rated -->
  <div>
    <v-row>
      <v-col>
        <h1>Top rated</h1>
      </v-col>
    </v-row>
    <v-row class="mb-0">
      <v-col v-for="rated in ratedArray" :key="rated.id" cols="12" md="12" lg="2">
        <v-card class="mx-auto" max-width="344">
          <v-img height="200px" :src="'https://image.tmdb.org/t/p/w500/' + rated.poster_path" cover></v-img>
          <v-card-title>{{ rated.title }}</v-card-title>
          <v-card-subtitle>{{ rated.release_date }}</v-card-subtitle>
          <v-card-actions>
            <v-btn color="orange-lighten-2" text>Explore</v-btn>
            <v-spacer></v-spacer>
            <v-btn :icon="show ? 'mdi-chevron-up' : 'mdi-chevron-down'" @click="show = !show"></v-btn>
          </v-card-actions>
          <v-expand-transition>
            <div v-show="show">
              <v-divider></v-divider>
              <v-card-text>{{ rated.overview }}</v-card-text>
            </div>
          </v-expand-transition>
        </v-card>
      </v-col>
    </v-row>
  </div>
  <!-- Popular -->
  <div>
    <v-row>
      <v-col>
        <h1>Series Populares</h1>
      </v-col>
    </v-row>
    <v-row class="mb-0">
      <v-col v-for="popular in popularArray" :key="popular.id" cols="12" md="12" lg="2">
        <v-card class="mx-auto" max-width="344">
          <v-img height="200px" :src="'https://image.tmdb.org/t/p/w500/' + popular.poster_path" cover></v-img>
          <v-card-title>{{ popular.name }}</v-card-title>
          <v-card-subtitle>{{ popular.first_air_date }}</v-card-subtitle>
          <v-card-actions>
            <v-btn color="orange-lighten-2" text>Explore</v-btn>
            <v-spacer></v-spacer>
            <v-btn :icon="show ? 'mdi-chevron-up' : 'mdi-chevron-down'" @click="show = !show"></v-btn>
          </v-card-actions>
          <v-expand-transition>
            <div v-show="show">
              <v-divider></v-divider>
              <v-card-text>{{ popular.overview }}</v-card-text>
            </div>
          </v-expand-transition>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<style lang="scss">
.widget-grid {
  > div {
    @media (max-width: 1540px) and (min-width: 1280px) {
      flex: 0 0 100%;
      max-width: 100%;
      text-align: center;
    }
  }
}
</style>
