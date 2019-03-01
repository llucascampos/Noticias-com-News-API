<template>
  <div>

    <v-layout justify-center mt-5>
      <v-flex xs4>
         <v-text-field
          v-model="pesquisa"
          label="Digite Sua Pesquisa"
          solo
          @keyup.enter="chamada()"
          ></v-text-field>
      </v-flex>
    </v-layout>
    
    <v-layout justify-center pb-5>
      <v-flex xs1>
        <v-btn dark  @click="chamada()">Pesquisar</v-btn>
      </v-flex>
    </v-layout>

    <div v-if="carregando" class="text-xs-center my-5">
      <v-progress-circular
      :size="100"
      color="primary"
      indeterminate
      > Carregando</v-progress-circular>
    </div>

    <div v-if="dadosNoticias !=null">
      <v-layout row justify-center mt-5>
        <v-flex xs9>
          <v-card  class="card" style="height: 500px" >
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
  </div>
</template>

<script>
import colors from 'vuetify/es5/util/colors'
import axios from 'axios'

export default {

  data(){
    return{
      pesquisa: null,
      dadosNoticias: null,
      carregando: false,

    } 
  },

  methods:{
    chamada(){
      this.carregando = true
      let url = `https://newsapi.org/v2/everything?q=${this.pesquisa}&apiKey=9a393de8a41246ffb3b265bf40820eee&language=pt`
       axios.get(url,
         { headers: { Authorization: '9a393de8a41246ffb3b265bf40820eee' } }
       )
       .then(Response => (
         this.dadosNoticias = Response.data.articles,
         this.carregando = false
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

.v-progess-circular{
  margin: 1rem
}

</style>
