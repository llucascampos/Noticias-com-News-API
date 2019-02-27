<template>
  <div>

    <v-layout justify-center mt-5>
      <v-flex xs6>
         <v-text-field
          v-model="pesquisa"
          label="Digite Sua Pesquisa"
          ></v-text-field>
          <v-btn @click="chamada()">Pesquisar</v-btn>
      </v-flex>
    </v-layout>
    <v-layout row justify-center mt-5>
      <v-flex xs10>
        <v-card  class="card" >
          <v-card-title><h2>Noticias</h2></v-card-title>
          <v-divider></v-divider>
          <div>
            <div v-for="noticia in dadosNoticias" :key="noticia.title">
              {{noticia.title}}
              <img :src="noticia.urlToImage" style="width: 100%">
              {{noticia.description}}
            </div>

          </div>
         
        </v-card>
      </v-flex>
    </v-layout>
  </div>
</template>

<script>
import axios from 'axios'

export default {

  data(){
    return{
      pesquisa: null,
      dadosNoticias: null,

    } 
  },

  methods:{
    chamada(){
      let url = `https://newsapi.org/v2/everything?q=${this.pesquisa}&apiKey=9a393de8a41246ffb3b265bf40820eee&language=pt`
       axios.get(url,
         { headers: { Authorization: '9a393de8a41246ffb3b265bf40820eee' } }
       )
       .then(Response => (
         this.dadosNoticias = Response.data.articles
       )) 

    }

  }


}
</script>

<style>

.card{
  height: 300px;
  overflow-x: hidden;
  overflow-y: scroll;
}

</style>
