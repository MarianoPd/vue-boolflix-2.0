<template>
  <div class="flip-card col-2 p-1">
    <div class="flip-card-inner">
        <div class="flip-card-front">
            <img  :src="sendResult.poster_path ? imgBaseUrl + sendResult.poster_path : ''" :alt="title">
        </div>
        <div class="flip-card-back d-flex flex-column">
            <div class="info ">
                <p><strong>Titolo: </strong>{{title}}</p>
                <img  :src="getFlag()" :alt="sendResult.original_language">
                <br>
                <i v-for="(item, index) in 5" :key="index"  :class="index < vote ? 'fas' : 'far'"  class="fa-star gold"></i>
                <p v-if="title !== originalTitle"><strong>Titolo originale: </strong>{{originalTitle}}</p>
                <p>
                    <strong>Genres</strong>
                    <span v-for="(genre,index) in genresList" :key="index">
                        {{genre + ', '}}
                    </span>
                </p>
                <p>
                    <strong>Cast</strong>
                    <span v-for="(member,index) in castArray" :key="index">
                        {{member.name + ', '}}
                    </span>
                </p>
                <p v-if="sendResult.overview !== ''" class="overview ">{{sendResult.overview}}</p>
            </div>
            <div >
                
            </div>
        </div>
       
    </div>    

  </div>
</template>

<script>
import axios from 'axios';

export default {
    name: 'Card',
    data(){
        return{
            imgBaseUrl: 'https://image.tmdb.org/t/p/w342',
            flagBaseUrl: 'https://flagcdn.com/w80/',
            castBaseUrl: 'https://api.themoviedb.org/3/',
            apiParams:{
                api_key: 'b9d6f32d855fdb9b296cc4a18dc951e7', 
            },
            castArray: [],
        }
    },
    props:{
        sendResult: Object,
        type: String,
        genresList: Array,
    },
    computed:{
        title: function(){
            if(this.type === 'movie') return this.sendResult.title;
            if(this.type === 'tv') return this.sendResult.name;
            return '';
            
        },
        originalTitle: function(){
            if(this.type === 'movie') return this.sendResult.original_title;
            if(this.type === 'tv') return this.sendResult.original_name;
            return '';
        },
        vote: function(){
            return Math.round(this.sendResult.vote_average /2);
        },
                
    },
    mounted(){
        this.getCastApi();
    },
    methods:{
        getCastApi(){
            axios.get(this.castBaseUrl + this.type + '/' + 
                        this.sendResult.id + '/credits',
                        {params: this.apiParams} )
            .then( r => {
                this.castArray = r.data.cast.splice(0,5);
            })
            .catch( e => {
                console.log('errore axios card',e);
            })
        },
        getFlag(){
            let language = this.sendResult.original_language;
            if(language === 'en') language = 'gb';
            if(language === 'ko') language = 'kr';
            if(language === 'hi') language = 'in';
            if(language === 'cs') language = 'cz';
            if(language === 'zh') language = 'cn';
            if(language === 'fa') language = 'ir';
            if(language === 'da') language = 'dk';
            if(language === 'el') language = 'gr';
            if(language === 'ja') language = 'jp';
            return this.flagBaseUrl + language + '.png';
        },
    
    },    
}    
</script>

<style scoped lang="scss">
@import '../assets/style/vars.scss';
@import '../assets/style/generals.scss';
 
    .flip-card {
        background-color: transparent;
        height: 320px;
        //border: 1px solid #f1f1f1;
        
        perspective: 1000px; 
        
        .flip-card-inner {
            position: relative;
            width: 100%;
            height: 100%;
            transition: transform 0.8s;
            transform-style: preserve-3d;
            .flip-card-front, .flip-card-back {
                position: absolute;
                width: 100%;
                height: 100%;
                -webkit-backface-visibility: hidden; 
                backface-visibility: hidden;
                border-radius: 10px;
                overflow: hidden;
                img{
                    color: white;
                    font-weight: bolder;
                    font-size: 20px ;
                    text-transform: uppercase;
                }
            }
            .flip-card-front {
                background-color: black;
                overflow: hidden;
                img{
                    max-height: 100%;
                    min-width: 100%;
                    text-align: center;
                }
            }
            .flip-card-back {
                background-color: black;
                color: white;
                transform: rotateY(180deg);
                padding: 15px 15px 0 15px;
                margin-left: 0;
                p{
                    font-size: 12px;
                }
                img{
                    width: 35px;
                    margin-bottom: 20px;
                }
                i{
                    color: gold;
                    font-size: 12px;
                }
                
                .overview, .info{
                    
                    overflow-y: auto;
                }
            } 
        }
        &:hover .flip-card-inner {
            transform: rotateY(180deg);
        }

    }

</style>