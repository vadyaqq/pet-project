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
    mainLoader,
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
.wrapper{
  background: #91FFAC;
  &:after{
    z-index: 0;
    content: "";
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-image: linear-gradient(45deg, #FFA591 25%, transparent 25%),
                      linear-gradient(-45deg, #FFA591 25%, transparent 25%),
                      linear-gradient(45deg, transparent 75%, #FFA591 75%),
                      linear-gradient(-45deg, transparent 75%, #FFA591 75%);
    background-size: 40px 40px;
    background-position: 0 0, 0 20px, 20px -20px, -20px 0px;
    animation: back 10s ease-in-out infinite alternate;
 
  }
  .page{
    margin: 1rem 0rem 3rem 0rem;
    z-index: 2;
    .page__main{
      margin-top: 2rem;
      .main__container{
        display: flex;
        flex-direction: column;
        align-items: center;
        background: #ffffff;
        min-height: 100vh;
        border-radius: 1em;
        outline: 2px inset #91FFAC;
        outline-offset: -10px;
        padding-bottom: 2rem;
        .main__title{
          font-size: 24px;
          text-transform: uppercase;
          padding: 2rem;
        }  
      }
    }
  }
}

  @keyframes back {
   0% {
    background-position: 0 0, 0 20px, 20px -20px, -20px 0px;
  }
  25% {
    background-position: 10px 10px, -10px -10px, 0 0, -20px -20px;
  }
  50% {
    background-position: 20px 20px, -20px -20px, 0 0, -40px -40px;
  }
  75% {
    background-position: 30px 30px, -30px -30px, 0 0, -60px -60px;
  }
  100% {
    background-position: 0 0, 0 20px, 20px -20px, -20px 0px;
  }
  }

</style>
