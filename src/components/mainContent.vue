<template>
  <swiper
    :direction="'horizontal'"
    :slides-per-view="1"
    :space-between="50"  
    navigation
    grabCursor
    :scrollbar="{ draggable: true }"
    class='swiper-default'
    :breakpoints="{
    1440: {
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
      :class="image.isSelected ? 'active' : ''"
    > 
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
    </swiper-slide>
  </swiper>
  
<nav class="content-container">
  <ul 
      v-if="selectedImages.length > 0"
      class="content__list" 
  >
      <li 
          v-for="(imageData, index) in selectedImages" 
          :key="index"
          class="content__list-item item-list" 
      >
          <div class="item-list__wrapper">
              <div class="item-list__author"><p>Author:</p><span> {{ imageData.author }}</span></div>
              <a class="item-list__download" :href="imageData.download_url">Lint to download</a>
              <button 
                  class="def-btn" 
                  @click="removeSelectedImage(imageData)"
              >
                  Delete
              </button>
          </div>
      </li>
  </ul>
  <div v-else class="content__listNone">Please select a photo</div>
</nav>
  
  
</template>

<script>
'use strict'
    
    // import photoList from "@/components/photoList"

    import { Swiper, SwiperSlide} from 'swiper/vue';
    import SwiperCore, { Navigation, Scrollbar } from "swiper";

    import "swiper/scss";
    import 'swiper/scss/navigation';
    import 'swiper/scss/scrollbar';
    // import 'swiper/scss/effect-fade';
    // import 'swiper/scss/effect-cube';
    // import 'swiper/scss/effect-cards';


    SwiperCore.use([Navigation, Scrollbar]);

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
                    return image.url !== undefined && image.url !== null;
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
        async onSlideClick(index) {
            const imageData = await this.images[index];
            imageData.isSelected = !imageData.isSelected;
            console.log(imageData.isSelected);
            if (imageData.isSelected) {
                this.selectedImages.push(imageData);
            } else {
                this.removeSelectedImage(imageData);
            }
            console.log(this.selectedImages);
        },
        removeSelectedImage(imageData) {
            const index = this.selectedImages.findIndex(choisePicture => choisePicture.id === imageData.id);
            console.log(index);
            if (index >= 0) {
                this.selectedImages.splice(index, 1);
            }
                imageData.isSelected = false;
        }
    },
}
</script>

<style scoped lang="scss">

.swiper{
  width: 98% !important;  
  .swiper-slide{
  border-radius: 15px;
  overflow: hidden;
  .image__wrapper{
      width: 100%;
      height: 10rem;
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
  &.active{
    border:1px solid #000000;
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
      transform: scale(1.2);
      cursor: pointer;
    }
    &:active{
      transform: translateZ(2px);
    }
  }
}
}

.content-container{   
    margin-top: 3rem;
    border-top: 2px solid #000000;
    .content__list{
      .content__list-item{
        .item-list__wrapper{
          display: flex;
          justify-content: center;
          align-items: center;
          flex-wrap: wrap;
          padding: 1rem 0;
          // padding: 1rem 2rem;
          .item-list__author,
          .item-list__download{
            padding: 0.7em 2em;
          }
          .item-list__download{
            border-right:1px solid black;
            border-left: 1px solid black;
            cursor: pointer;
          }
          .item-list__author{
            display: flex;
            flex-direction:row;
            justify-content: center;
            align-items: center;
            align-self:center;
            p{
                margin-right: .5rem
            }
          }
          .def-btn{
            margin: 0 2rem;
          }
        }
      }
    }
    .content__listNone{
        padding: 1rem 0;
        text-transform: uppercase;
    }
}

@media (max-width: 603px){
.content-container{   
    .content__list{
      .content__list-item{
        .item-list__wrapper{
          // padding: 1rem 2rem;
          .item-list__author,
          .item-list__download{
            text-align: center;
          }
          .item-list__author{
            display: flex;
            flex-direction:column;
            align-items:center;
            p{
                margin-right: 0;
                margin-bottom: .5rem;
            }
          }
          .def-btn{
            margin: 0 2rem;
          }
        }
      }
    }
    .content__listNone{
        text-transform: uppercase;
    }
}
}

@media (max-width: 466px){
.content-container{   
    .content__list{
      .content__list-item{
        .item-list__wrapper{
            border-bottom: 1px solid #000000;
          .item-list__author,
          .item-list__download{
            text-align: center;
          }
          .item-list__author{
            display: flex;
            flex-direction:column;
            align-items:center;
            p{

            }
          }
          .def-btn{
            margin-top: 1rem;
          }
        }
      }
    }
    .content__listNone{
        text-transform: uppercase;
    }
}
}
</style>


