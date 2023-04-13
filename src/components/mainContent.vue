<template>
  <swiper
    :direction="'horizontal'"
    :slides-per-view="1"
    :space-between="40"  
    :speed="800"
    grabCursor
    :centeredSlides="true"
    :navigation="{
        nextEl: '.swiper-button-next',
        prevEl: '.swiper-button-prev',
    }"
    :pagination="{
        type: 'bullets',
        dynamicBullets: 'true',
        clickable: 'true',
    }"
    :style="{
        '--swiper-navigation-size': '24px',
        '--swiper-pagination-color': '#000000',
        '--swiper-pagination-bottom': '0px',
        '--swiper-pagination-bullet-size': '14px',
        '--swiper-pagination-bullet-width': '12px',
        '--swiper-pagination-bullet-height': '12px',
    }"
    :breakpoints="{
        1580: {
          slidesPerView: 4,    
        },
        1240: {
          slidesPerView: 4,    
        },
        992: {
          slidesPerView: 3,    
        },
        576: {
          slidesPerView: 2,
        },
    }"
  > 
    <swiper-slide
      v-for="(image,index) in images"
      :key="`img-${image.id}`"
      v-slot="{ isActive, isPrev, isNext }"
      :class="image.isSelected ? 'active' : ''"
    > 
      <div 
        class="slide" 
        :class="{ 'slide-active': isActive, 'slide-prev': isPrev, 'slide-next': isNext }"
      >
        <!-- <div class="slide-content"> -->
          <input 
            type="checkbox"
            class="checkbox" 
            @click="onSlideClick(index)"
            v-model="image.isSelected"
          > 
          <div
            class="image__wrapper"
          >
            <img
              :src="image.download_url"
              alt=""
            />
          </div>
        <!-- </div> -->
      </div>
    </swiper-slide>
    <div class="swiper-button-prev"></div>
    <div class="swiper-button-next"></div>
  </swiper>

  
<nav class="content-container">
  <ul       
      class="content__list" 
  >
      <li 
          v-for="(imageData, index) in selectedImages" 
          :key="`selectedImages-${index}`"
          class="content__list-item item-list"
          :class="imageData.isSelected ? '' : 'delete'"
          ref="listItem"
      >
              <div class="item-list__author"><b>Author: </b><span> {{ imageData.author }}</span></div>
              <a class="item-list__download" :href="imageData.download_url">Link to download</a>
              <button 
                  class="def-btn" 
                  @click="removeSelectedImage(imageData)"
              >
                  Delete
              </button>
      </li>
  </ul>
  <h2 v-show="this.selectedImages.length === 0" class="content__listNone">Please select a photo</h2>
</nav>
  
  
</template>


<script>
'use strict'
    
    import gsap from 'gsap';
    // import photoList from "@/components/photoList"

    import { Swiper, SwiperSlide} from 'swiper/vue';
    import SwiperCore, { Navigation, Pagination } from "swiper";

    import "swiper/scss";
    import 'swiper/scss/navigation';
    import 'swiper/scss/pagination';



    SwiperCore.use([Navigation, Pagination]);

export default {
    name: 'photoSlider',
    props: {
        images: {
            type: Array,
            required: true,
            default: () => {
                return [];
            },
            validator: (images) => {
                return images.every((image) => {
                    return image.download_url !== undefined && image.download_url !== null;
                });
            },
        },
    },
    data() {
        return {
            selectedImages: [],
        }
    },
    components: {
        // photoList,
        Swiper,
        SwiperSlide,
    },
    methods: {
        onSlideClick(index) {
          const imageData = this.images[index];
          imageData.isSelected = !imageData.isSelected;
          if (imageData.isSelected) {
            this.selectedImages.push(imageData);
            this.$nextTick(() => {
              const listItem = this.$refs.listItem[this.selectedImages.length - 1];
                gsap.from(listItem, {
                  x: -200,
                  opacity: 0,
                  height: 0,
                  duration: .7,
                });
            });

          } else {
            this.removeSelectedImage(imageData);
          }
        },               
        removeSelectedImage(imageData) {
            const index = this.selectedImages.findIndex((choisePicture) => choisePicture.id === imageData.id);
            if (index >= 0) {
              imageData.isSelected = false;
              setTimeout(() => {
                this.selectedImages.splice(index, 1);
              }, 700);   
            }
    }
  }
}
</script>

<style scoped lang="scss">

.swiper{
  width: 100%;
  padding: 2rem 1rem;
  .swiper-slide{
    height: 150px;
    .slide {
      width: 100%;
      height: 100%;
      box-shadow: 0px 0px 28px 7px #000000;
      transition: all .8s ease-out;
      border-radius: 1rem;
      overflow: hidden;
      transform: scale(.7) rotateY(360deg);
      will-change: transform;
      filter: blur(4px);
      &.slide-active {
          box-shadow: 0px 0px 9px 7px #000000;
          border: 1px solid #ffffff;

          transform: scale(1) rotateY(0deg);
          filter: blur(0);  
      }
      &.slide-prev {
          transform: scale(.7) rotateY(0deg);
          filter: blur(0); 
      }
      &.slide-next {
          transform: scale(.7) rotateY(0deg);
          filter: blur(0); 
      }
      .image__wrapper{
        width: 100%;
        height: 100%;
        position: relative;
          img {
              position: absolute;
              content: "";
              left: 0;
              top: 0;
              width: 100%;
              height: 100%;
              object-fit: cover;
          }
      }
      .checkbox{
        position: absolute;
        content: "";
        z-index: 99;
        top: 20px;
        right: 20px;
        width: 1.2rem;
        height: 1.2rem;
        transition: all .5s ease 0s;
        &:hover{
          cursor: pointer;
        }
        &:active{
          transform: translateZ(-2px);
        }
      }
    }
  }
  .swiper-button-prev,
  .swiper-button-next{
      height: 40px;
      width: 40px;
      top: calc(50% - 25px);
      color: #ffffff;
      background: #000000;
      border-radius: 50%;
      margin-top: 0;
      padding: 0 5px;
      transition: transform .5 ease 0;
      &:hover{
          transform: scale(1.05);
          &:after{
              font-weight: 900;
          }
      }
      &:after{
          padding: 5px 5px;
      }
  }

  .swiper-button-prev{
      &:active{
          &:after{
              transform: translateX(-4px);
          }
      }   
  }
  .swiper-button-next{
      &:active{
          &:after{
              transform: translateX(4px);
          }
      }
  }

}


.content-container{
  width: 100%;
  padding: 2rem 1rem;
  text-align: center;
  .content__list-item,
  .item-list{
    display: flex;
    flex-wrap:wrap;
    align-items: center;
    justify-content: space-evenly;
    padding: .5em 0;
    border-top: 1px solid #000;
    &.delete{
      animation: shake 0.7s

    }
    .item-list__author{
      width: 200px;
      padding: .8em 0;
      text-align: left;
    }
    .item-list__download{
      position: relative;
      padding: .8em 1em;
      color:#000;
      cursor: pointer;
      &:after{
        content: '';
        position: absolute;
        right: 50%;
        bottom: 0;
        width: 0;
        height: 1px;
        background-color: #000;
        transition: all 0.5s ease;
        transform: translateX(50%);  
      }
      &:hover::after{
        width: 100%;
      }
      &:active{
        transform: translateY(-2px);
      }
    }
  }
  .content__listNone{
    text-transform: uppercase;
    animation:  10s ease-in-out infinite alternate;

  }
}
@keyframes shake {
  0% {
    transform: translate(0px, 0px);
  }
  10% {
    transform: translate(-5px, 0px);
  }
  20% {
    transform: translate(-10px, 0px);
  }
  30% {
    transform: translate(-15px, 0px);
  }
  40% {
    transform: translate(-10px, 0px);
  }
  50% {
    transform: translate(-5px, 0px);
    opacity: .5;
  }
  60% {
    transform: translate(0px, 0px);
    opacity: .4;
  }
  70% {
    transform: translate(10px, 0px);
    opacity: .3;
  }
  80% {
    transform: translate(20px, 0px);
    opacity: .2;
  }
  90% {
    transform: translate(30px, 0px);
    opacity: .1;
  }
  100% {
    transform: translate(40px, 0px);
    opacity: 0;
  }
}

@media (max-width: 426px) {
.content-container{
  .content__list-item{
    .item-list__author{
      text-align: center;
    }
    .item-list__download{

    }
  }
}
}

</style>


