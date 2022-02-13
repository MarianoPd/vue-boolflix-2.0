<template>
  <header class="container-fluid d-flex justify-content-between ">
    <img src="../assets/img/netflix_logo.svg" alt="logo">
    <div class=" my-form d-flex " >
      <input v-model="search" type="text"
              @keyup.enter="sendSearch()" 
              class="form-control  h-50">
      <button @click="sendSearch()" class="btn btn-light h-50">
        Search
      </button>
    </div>
  </header>
</template>

<script>

export default {
  
  name: 'Header',
  data(){
    return{
      search: '',  
    }
  },
  methods:{
    sendSearch(){
      this.$emit('sendSearch', this.search);
      this.search = '';
    }  
  }
}
</script>


<style scoped lang="scss">
@import '../assets/style/vars.scss';
@import '../assets/style/generals.scss';

header{
  height: 65px;
  background: linear-gradient(rgba(0,0,0,100), rgba(0,0,0,0));
  position: fixed;
  top: 0;
  z-index: 99;
  img{
    height: 100%;
    margin-left: 20px;
  }
  .my-form{
    width: 20%;
    height: 100%;
    input,button{
      margin: 25px 20px 0 0;
    }
  }
}
</style>
