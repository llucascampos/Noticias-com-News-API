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

    <v-layout justify-center>
      <v-flex xs3 pr-5>
        <v-text-field
        label="De:"
        v-model="from"
      >
      </v-text-field>
      </v-flex>

      <v-flex xs3 pl-5>
        <v-text-field
        label="Até:"
        v-model="to"
        >
        </v-text-field>
      </v-flex>
    </v-layout>

    <div v-if="dadosNoticias !=null">
      <v-layout row justify-center mt-5>
        <v-flex xs9>
          <v-card>
            <v-card-title class="py-4"><h2>Noticias sobre: {{termoPesquisado}}</h2></v-card-title>
            <v-divider></v-divider>
            <div class="card">
              <div v-for="noticia in dadosNoticias" :key="noticia.title" class="noticias">
                <h1>{{noticia.title}}</h1>
                <a :href="noticia.urlToImage" target="_blank"> <img :src="noticia.urlToImage"></a>
                <p>{{noticia.description}}</p>
                <a :href="noticia.url" target="_blank">Leia mais</a>
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
      termoPesquisado: null,
      from: '',
      to: '',

    } 
  },

  methods:{
    chamada(){
      this.carregando = true
      let url = `https://newsapi.org/v2/everything?q=${this.pesquisa}&from=${this.from}&to=${this.to}&apiKey=9a393de8a41246ffb3b265bf40820eee&language=pt`
      axios.get(url,
        { headers: { Authorization: '9a393de8a41246ffb3b265bf40820eee' } }
      )
       .then(Response => (
         this.dadosNoticias = Response.data.articles,
         this.carregando = false,
         this.termoPesquisado = this.pesquisa
       )) 

    }

  }


}
</script>

<style>

.card{
  height: 600px;
  overflow-x: hidden;
  overflow-y: scroll;
  padding: 50px;
}

.v-progess-circular{
  margin: 1rem;
}

.noticias{
  text-align: center;
  padding: 50px;
  border: 1px black solid;
  border-radius: 10px;
  margin-top: 50px;
}
.noticias img{
  width: 100%;
  padding: 20px 0px;
}

.noticias p{
  font-size: 20px;
}

</style>
