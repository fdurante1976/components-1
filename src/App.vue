<script setup>
  import { ref, computed } from 'vue';

  import BlogPost from './components/BlogPost.vue'
  import PaginatePost from './components/PaginatePost.vue';
  import LoadingSpinner from './components/LoadingSpinner.vue';

  const posts = ref([]);
  const postXpage = 10;
  const inicio = ref(0);
  const fin = ref(postXpage);
  const loading = ref(false);

  const favorito = ref(' ');

  const next = () => {
    inicio.value = inicio.value + postXpage;
    fin.value = fin.value + postXpage;
  }

  const prev = () => {
    inicio.value = inicio.value - postXpage;
    fin.value = fin.value - postXpage;
  }

  const cambiarFavorito = ( post ) => {
    favorito.value = post;
  }  

  const fetchData =  async() => {
    loading.value = true;
    try {
      const res = await fetch('https://jsonplaceholder.typicode.com/posts');
      posts.value = await res.json();
    } catch (error) {
      console.log( error );
    } finally {
      setTimeout(() => {
        loading.value = false
      }, 2000);
    }
  };

  fetchData();

  
  const maxLength = computed(() => posts.value.length);
</script>

<template>

  <LoadingSpinner v-if="loading"/>

  <div class="container" v-else>
    <h1>APP</h1>
    <h2>Mis Post Favoritos: {{ favorito }}</h2>

    <PaginatePost 
      @next = "next"
      @prev = "prev"
      class = "mb-2"
      :inicio = "inicio"
      :fin = "fin"
      :maxLength = "maxLength"
    />
    <BlogPost 
      v-for="post in posts.slice(inicio,fin)"
      :key = 'post.id'
      :title = 'post.title'
      :id = 'post.id'
      :body = 'post.body'
      @cambiarFavoritoNombre = "cambiarFavorito"
      class = "mb-2"
    />
  </div>
</template>