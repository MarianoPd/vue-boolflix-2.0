<template>
  <div id="app">
    <Header @sendSearch="setMovieSearch"
            />
    <Main :showMovieResult="movieResults" :showSerieResult="serieResults"
            />
  </div>
</template>

<script>
import axios from 'axios';
import Header from './components/Header.vue'
import Main from './components/Main.vue'

export default {
  name: 'App',
  components: {
    Header,
    Main,
  },
  data(){
    return{
      movieResults: [],
      serieResults: [],
      apiUrl: 'https://api.themoviedb.org/3/search',
      popularApiUrl: 'https://api.themoviedb.org/3/discover',
      apiParams:{
        api_key: 'b9d6f32d855fdb9b296cc4a18dc951e7',
        language: 'it-IT',
        query: ''
      },
      isLoaded: false,
      //tentativo riproduzione video
      videoUrl: 'https://api.themoviedb.org/3/movie/',
      movieId: '',
      videos: [], 
    }
  },
  methods:{
    setMovieSearch(text){
      this.apiParams.query = text;
      this.getApi(this.apiUrl);
      console.log('app result',this.apiParams.query);
    },
    getApi(url){
      this.isLoaded = false;
      
      if(this.searchMovieString !== ''){
        axios.all([this.requestMovie(url),this.requestSerie(url)])
          .then(axios.spread((movies, series) =>{
            this.isLoaded = true;
            this.movieResults = movies.data.results;
            this.serieResults = series.data.results;
            //ora prendo il video del primo film
            this.movieId = this.movieResults[0].id;
             //this.requestVideo(this.movieId);
            
            console.log('axios results',movies, series);            
          }))
          .catch( e => {
            console.log('axios error',e);
          })
      }
    },
    requestMovie(url){
      return axios.get(url + '/movie',{params: this.apiParams});
    },
    requestSerie(url){
      return axios.get(url + '/tv',{params: this.apiParams});
    },

    requestVideo(movieId){      
       axios.get(this.videoUrl + movieId + '/videos',{params: this.apiParams})
            .then(res=>{
              this.videos = res;
              console.log('videos ',this.videos);
            });

    }

  },
  mounted(){
    this.getApi(this.popularApiUrl);    
  }

}
</script>

<style lang="scss">
@import './assets/style/vars.scss';
@import './assets/style/generals.scss';



</style>
