<template>
  <div class="w-full h-full min-h-screen py-6 flex flex-col md:flex-row md:fixed top-0 left-0">
    <div class="flex flex-col md:ml-10 w-full h-auto md:w-2/5" id="l__panel">
      <p class="text-center text-gray-800 py-2" id="title">Image</p>
      <div class="w-full h-full bg-transparent relative p-0 mx-auto my-auto" id="l__panel-image">
        <img class="absolute w-full h-1/2 top-0 left-0 object-cover bg-center" v-if="url" :src="url" alt="image not available">
      </div>
    </div>
    <div class="w-full h-full md:w-3/5 px-2 md:px-20 mx-auto p-0 py-6 md:fixed top-0 right-0 overflow-hidden overflow-y-scroll">
      <div v-if="loading" class="px-2 w-full py-10 flex justify-center mx-auto">
        <div class="looping-rhombuses-spinner">
          <div class="rhombus"></div>
          <div class="rhombus"></div>
          <div class="rhombus"></div>
        </div>
      </div>
      <div v-else class="px-2 w-full mb-10" id="translated__text">
        <p class="md:px-4">
          {{ text }}
        </p>
      </div>
      <p class="text-xl font-black uppercase pb-4">extract text from images</p>
      <form class="w-full mx-auto p-0" @submit.prevent="submitForm" enctype="multipart/form-data">
        <div class="relative p-0 w-full md:w-4/5 md:mx-auto h-40 flex justify-center items-center" id="file__input">
          <input class="absolute top-0 left-0 w-full h-full outline-none opacity-0" type="file" @change="handleFileSelect" ref="file"/>
          <p>Drag your image here or <span class="text-blue-900 font-semibold">click</span> in this area.</p>
        </div>
        <div class="flex flex-col md:flex-row justify-center w-full mt-16">
          <div class="flex flex-col mx-auto md:mx-4 mb-4 md:mb-1">
              <label class="block text-blue-800 text-sm font-semibold mb-2 uppercase">
                source language
              </label>
              <select v-model="src_lang" class="border bg-transparent border-black text-blue-900 uppercase px-2 py-2 md:mx-2 focus:outline-none w-full">
                <option>autodetect</option>
                <option v-for="(value, propertyName) in cclst" v-bind:key="propertyName">
                  {{ propertyName }}
                </option>
              </select>
          </div>
          <div class="flex flex-col mx-auto md:mx-4 mb-4 md:mb-1">
              <label class="block text-blue-800 text-sm font-semibold mb-2 uppercase">
                destination language
              </label>
            <select v-model="dst_lang" class="border bg-transparent border-black text-blue-900 uppercase px-2 py-2 md:mx-2 focus:outline-none w-full">
                <option>autodetect</option>
                <option v-for="(value, propertyName) in cclst" v-bind:key="propertyName">
                  {{ propertyName }}
                </option>
            </select>
          </div>
        </div>
        <div class="my-6">  
          <button class="mx-auto px-4 py-2 font-semibold text-white outline-none" type="submit">
            convert
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
const axios = require('axios');

export default {
  name: 'Landing',
  data(){
    return {
      cclst: null,
      text: 'YOUR SUBCONSCIOUS THE POWER ol your subconscious is enormous. it inspires you, it guides you, and it reveals to you names, facts, and scenes from the storehouse of memory. your subconscious started your heartbeat, controls the circulation of your blood, and regulates your digestion, assimilation, and elimination. When you eat a piece of bread, your subconscious mind tiansmutes it into tissue, muscle, bone, and blood. This process is beyond the ken of the wisest man who walks the earth.',
      file: null,
      url: 'subcounciouss.jpg',
      src_lang: 'autodetect',
      dst_lang: 'autodetect',
      loading: false
    }
  },
  methods: {
    async submitForm() {
      //window.console.log(this.src_lang, this.dst_lang);
      if(this.file){      
        const formData = new FormData();
        formData.append('image', this.file);
        formData.append('src_lang', this.src_lang);
        formData.append('dst_lang', this.dst_lang);

        try {
          this.loading = true;
          window.console.log(this.loading);
          const r = await axios.post(`${this.SERVER}/api/v1/upload`, formData);
          this.text = await r.data;
          window.console.log(this.text);
          this.loading = false;
          window.console.log(this.loading);
        } catch(err) {
          window.console.log(`%cError => ${err}`, 'color: red'); 
        }
      } else {
        window.console.log('%cError => NO FILE SELECTED','color: red');
      }
    },
    async handleFileSelect(){
      this.file = await this.$refs.file.files[0];
      this.url = URL.createObjectURL(this.file);
      //window.console.log(this.file.name);
    }
  },
  mounted(){
    /**/
    // Get available languages
    axios.get(`${this.SERVER}/api/v1/available_lang`)
    .then( res => { 
      //window.console.log(res);
      this.cclst = res.data;
    })
    .catch(err => {
      window.console.log('%cError => ', err);
    })
    /**/
  },
  props: {
    SERVER: String
  }
};

</script>


<style scoped>
  
  :root {
    --dbl: #123C69;
  }

  ::selection {
    background-color: pink;
    color: black;
  }

  ::-moz-selection {
    background-color: pink;
    color: black;
  }

  li {
    list-style: none;
    display: flex;
    flex-direction: row;
    justify-content: center;
    margin: 0 4em 0 4em;
  }

  #id {
    display: inline-block;
    transform: rotate(-90deg);
    margin-right: 2em;
  }

  p {
    color: #123C69;
  }

  #al {
    color: #AC3B61;
  }
  
  @media screen and (max-width: 768px){
    #l__panel-image {
      min-height: 80vh; 
    }
  }

  #l__panel-image {
    max-height: 80vh;
  }

  #file__upload {
    width: 90%;
  }


  #file__input {
    background-color: #EEE2DC;
    border: 1.3px dashed #123C69;
  }

  #translated__text {
    background-color: #EEE2DC;
    border-radius: 10px;
    padding: 2em;
  }

  #title,
  #note {
    margin-top: 1.2rem;
    font-size: .7em;
    padding: .9em;
  }

  #translated__text::before {
    content: 'Translated text';
    font-size: .7em;
    padding: .9em;
  }

  form button{
    background: #AC3B61;
    border-radius: 4px;
    border-bottom: 4px solid #773E51;
    transition: all .2s ease;
  }

  form button:hover {
    color: #DEC3CC;
  }

  form button:active{
    border:0;
  }

  /* Spinner */
  .looping-rhombuses-spinner, .looping-rhombuses-spinner * {
        box-sizing: border-box;
      }

      .looping-rhombuses-spinner {
        width: calc(15px * 4);
        height: 15px;
        position: relative;
      }

      .looping-rhombuses-spinner .rhombus {
        height: 15px;
        width: 15px;
        background-color: #123C69;
        left: calc(15px * 4);
        position: absolute;
        margin: 0 auto;
        border-radius: 2px;
        transform: translateY(0) rotate(45deg) scale(0);
        animation: looping-rhombuses-spinner-animation 2500ms linear infinite;
      }

      .looping-rhombuses-spinner .rhombus:nth-child(1) {
        animation-delay: calc(2500ms * 1 / -1.5);
      }

      .looping-rhombuses-spinner .rhombus:nth-child(2) {
        animation-delay: calc(2500ms * 2 / -1.5);
      }

      .looping-rhombuses-spinner .rhombus:nth-child(3) {
        animation-delay: calc(2500ms * 3 / -1.5);
      }

      @keyframes looping-rhombuses-spinner-animation {
        0% {
          transform: translateX(0) rotate(45deg) scale(0);
        }
        50% {
          transform: translateX(-233%) rotate(45deg) scale(1);
        }
        100% {
          transform: translateX(-466%) rotate(45deg) scale(0);
        }
      }
</style>
