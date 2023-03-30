<template>
  <div class="wrapper">
    <main class="page" v-if="!isLoading">
      <section class="page__main main">
        <div class="main__container">
          <h1 class="main__title"> vadym tokovchuk </h1>
          <main-content
            :images="images"
          /> 
        </div>
      </section>
    </main>
    <main-loader v-else/>
  </div>
</template>

<script>
'use strict'
import axios from 'axios';

import mainContent from '@/components/mainContent';
import mainLoader from '@/components/mainLoader';

export default {
  name: 'App',
  components: {
    mainContent,
    mainLoader
  },
  data() {
    return {
      isLoading: true,
      images: [],
    };
  },
  methods: {
    async getImages() {
      try { 
        const response = await axios.get('https://picsum.photos/v2/list?page=2&limit=50');
        this.images = response.data;
        this.isLoading = false;
      } catch (error) {
            if (error.response) {
              // Request made but the server responded with an error
              console.log(error.response.data);
              console.log(error.response.status);
              console.log(error.response.headers);    
            } else if (error.request) {
              // Request made but no response is received from the server.
              console.log(error.request);  
            } else {
              // Error occured while setting up the request
              console.log('Error', error.message); 
            }
      }
    },
  },
  created() {
    this.getImages();
  },

}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;

}

.page{
  margin-top: 1rem;
  .page__main{
    margin-top: 2rem;
    .main__container{
      display: flex;
      flex-direction: column;
      align-items: center;
      .main__title{
        font-size: 24px;
        text-transform: uppercase;
        padding: 2rem;
      }  
    }
  }
}
</style>
