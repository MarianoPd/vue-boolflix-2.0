<template>
  <main >
    <div class="jumbo">

    </div>


    <div class="container pb-5">
      <div v-if="(showMovieResult.length !== 0) || (showSerieResult.length !== 0)">

        <div class="d-flex justify-content-between sub-header">
          <h3><strong>Movies</strong></h3>
          <select v-model="selectedMovieGenre">
            <option value="">Genre</option>
            <option v-for="(genre, index) in genreMovieList" :key="index" :value="genre">{{genre.name}}</option>
          </select>
        </div>

        <h1 v-if="showMovieResult.length === 0" class="loading">There are no movies with this name</h1>
        <div v-else class="row flex-nowrap no-gutters mb-1 my-row">
          <Card  v-for="(result, index) in getFilteredList(showMovieResult, selectedMovieGenre)"
              :key="index"
              :sendResult="result"
              :genresList="sendGenres(genreMovieList, result)"
              :type="'movie'"/>
        </div>

        <div class="d-flex justify-content-between sub-header">
          <h3><strong>Tv Shows</strong></h3>
          <select v-model="selectedTvGenre">
            <option value="">genre</option>
            <option v-for="(genre, index) in genreTvList" :key="index" :value="genre">{{genre.name}}</option>
          </select>
        </div>

        <h1 v-if="showSerieResult.length === 0" class="loading">There are no series with this name</h1>
        <div v-else class="row flex-nowrap no-gutters mb-1 my-row">    
          <Card  v-for="(result, index) in getFilteredList(showSerieResult,selectedTvGenre)"
              :key="index"
              :sendResult="result"
              :genresList="sendGenres(genreTvList, result)"
              :type="'tv'"/>
        </div>        
      </div>
      <h1 v-else class="loading">Loading results</h1>
    </div>
  </main>
</template>

<script>
import axios from 'axios';
import Card from './Card.vue';
export default {
    name: 'Main',
    components:{
      Card,
    },
    props:{
      showMovieResult: Array,
      showSerieResult: Array,
      
    },
    data(){
      return{
        genreMovieList: [],
        genreTvList: [],
        selectedMovieGenre: '',
        selectedTvGenre: '',
        genreListUrl: 'https://api.themoviedb.org/3/genre',
        apiParams:{
          api_key: 'b9d6f32d855fdb9b296cc4a18dc951e7', 
        },
      }
    },
    mounted(){
      this.getGenresApi(this.genreListUrl);
      this.$emit
    },
    methods:{
      getFilteredList(results, genre){
        let newList = [];
        console.log('genre',genre);
        
        if(genre === '') return results;
        for(let i = 0; i< results.length; i++){
          if(results[i].genre_ids.includes(genre.id)){
            newList.push(results[i]);
          }
        }
        console.log('new list' , newList);
        return newList;
      },
      
      getGenresApi(url){
      axios.all([this.requestMovieGenres(url),this.requestTvGenres(url)])
        .then(axios.spread((moviesGen, seriesGen) =>{
          this.genreMovieList = moviesGen.data.genres;
          this.genreTvList = seriesGen.data.genres;
          console.log('list genre',this.genreMovieList,this.genreTvList);
        }))
        .catch( e => {
            console.log('axios error list',e);
        })
      },
      requestMovieGenres(url){
        return axios.get(url + '/movie/list',{params: this.apiParams});
      },
      requestTvGenres(url){
        return axios.get(url + '/tv/list',{params: this.apiParams});
      },
      sendGenres(list, object){
        let genres = [];
        for(const element of list){
          if(object.genre_ids.includes(element.id)){
            genres.push(element.name);
          }
        }
        return genres;
      }
    }
    
}
</script>

<style scoped lang="scss">
@import '../assets/style/vars.scss';
@import '../assets/style/generals.scss';

main{
    min-height: calc(100vh - 100px);
    background:linear-gradient($primary-color, darken($primary-color,10%));
    .sub-header{
      color: white;
      padding-top: 100px;
      select{
        height: 30px;
        width: 200px;
      }
    }
    .loading{
      padding-top: 200px;
      color: white;
    }
    .my-row{
      width: 100%;
      overflow-y: hidden;
      
    }
    
}
</style>