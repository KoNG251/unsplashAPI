<template>
  <div>
    <div class="box">
      <h1 class="text-3xl font-bold mb-3">{{message}}</h1>
      <label class="input">
        <svg class="h-[1em] opacity-50" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><g stroke-linejoin="round" stroke-linecap="round" stroke-width="2.5" fill="none" stroke="currentColor"><circle cx="11" cy="11" r="8"></circle><path d="m21 21-4.3-4.3"></path></g></svg>
        <input
          type="search"
          class="grow"
          placeholder="Search picture"
          v-model="search"
          @keyup.enter="searchImage"
        />
      </label>
    </div>
    <div :class="`mt-10`">
      <div v-if="error_message">
        <h4 class="text-center text-2xl font-bold">No images found from this keyword<br> [{{ keyword }}]</h4>
      </div>
      
      <div class="columns-1 sm:columns-2 md:columns-3 lg:columns-4 gap-4 mb-6" v-else>
        <div
          v-for="item in items"
          :key="item.id"
          class="relative mb-4 rounded-lg overflow-hidden"
          @click="openModal(item)"
        >

          <img
            :src="item.urls.small"
            :alt="item.alt_description"
            class="w-full h-auto rounded-lg shadow-lg hover:scale-105 transition"
          />
        </div>
      </div>

      <div v-if="is_result" class="text-center">
        <button class="btn btn-outline" @click="loadmoreImage">Load more</button>
      </div>
    </div>

    <dialog id="image-modal" class="modal">
      <div class="modal-box">
        <button class="btn btn-sm btn-circle absolute right-2 top-2" onclick="document.getElementById('image-modal').close()">✕</button>
        <img v-if="selectedImage" :src="selectedImage.urls.regular" class="w-full rounded-lg" />
      </div>
      <form method="dialog" class="modal-backdrop">
        <button>close</button>
      </form>
    </dialog>

  </div>
</template>

<style scoped>
  .box{
    display: flex;
    flex-direction: column;
    width: 100%;
    justify-content: center;
    align-items: center;
  }

  .columns-2, .columns-3, .columns-4 {
    column-gap: 1rem;
  }
</style>

<script>
import axios from 'axios';

export default{
  data(){
    return{
      message: 'UNSPLASH',
      search: '',
      items: [],
      per_page : 8,
      is_result: false,
      error_message: false,
      keyword : null,
      is_loading: false,
      selectedImage: null
    }
  },
  methods:{
    searchImage(){

      if(this.keyword != this.search){
            this.keyword = this.search
            this.per_page = 8
        }

      axios.get('https://api.unsplash.com/search/photos',
       {
        params: {
          query: this.search,
          per_page: this.per_page,
        },
        headers: {
          Authorization: `Client-ID 7q7pQaV2uDil-lkyNthO229shEyFqNaOe5cvt7n3iCU`,
        }
       }
      ).then(res => {
        if(res.data.total === 0){
          this.error_message = true
          this.is_loading = false
          this.is_result = false
        }else{
          this.error_message = false
          this.items = res.data.results
          this.is_result = true
          this.is_loading = false
        }
      }).catch(error => {
        console.log(error);
      })
    },
    loadmoreImage(){
      this.is_loading = true
      if(this.per_page === 24){
        this.per_page = 8
      }else{
        this.per_page += 8
      }
      this.searchImage()
    },
    openModal(item){
      this.selectedImage = item
      document.getElementById('image-modal').showModal()
    }
  }
}
</script>