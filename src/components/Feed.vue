<template>
<div>
    <v-toolbar  class="py-4" >
      <div style="width: 100%;text-align: center;">
        <img src="../assets/logomultiedro.png">
      </div>
    </v-toolbar>

    <div class="big-header">
      <v-container fluid >
        <v-layout row wrap>

          <v-flex sm12   md6>
            <h1 class="titulo-page">Machine Learning</h1>
            <p class="desc-page">Demo de Machine Learning + Natural Language Processing</p>
          </v-flex>
          <v-flex ></v-flex>
          <v-flex sm12  md5 class="pt-5">
              <v-text-field
                label="Digite Sua Pesquisa"
                v-model="pesquisa"
                color="white"
                class="input-texto"
                dark
                @keyup.enter="chamandoDados(), ativandoScroll()"
              ></v-text-field>
              <div style="text-align:right;">
                <v-btn
                @click="chamandoDados(), ativandoScroll()"
                color="#7b2ede" dark style="margin-top: 0px !important;"
                >Pesquisar</v-btn>
              </div>
          </v-flex>
        </v-layout>
      </v-container>
    </div>

<v-container fluid>

  <!-- Mostrando termo pesquisado -->
     <v-layout justify-center wrap v-if="termoPesquisado" class="mb-4">
        <v-flex xs12>
          <h2 class="termo-titulo">Termo Pesquisado:</h2>
        </v-flex>
        <v-flex xs12>
          <h1 class="termo-pesquisado"><span class="hashtag"><v-icon>search</v-icon></span>{{ termoPesquisado }}</h1>
        </v-flex>
      </v-layout>

      <!-- Mostrando carregamento -->
      <v-layout justify-center class="my-5" v-if="carregando">
          <v-progress-circular
          indeterminate
          color="purple"
          :size="90"
        ><span class="loading-meu-item">Carregando</span>
        </v-progress-circular>
      </v-layout>

    <!-- Conteudo -->

    <v-layout row wrap>

  <!-- Card Twitter -->
      <v-flex sm12 md6  class="pa-3 text-md-center"  v-if="scoreTwitter != null">
        <v-card class="px-2">
            <v-card-title><img src="../assets/twitter.png" style="height:25px"><h3 class="ml-3">Twitter</h3></v-card-title>
            <v-divider class="py-2"></v-divider>
            <div class="cardSocial">

              <div v-if="dadosTwitter == ''" style="text-align: center">
                <h3>Dados Não Encontrados</h3>
              </div>

              <div v-else v-for="tweet in dadosTwitter" :key="tweet.id"  style="width: 100%" class="text-xs-center">
                <tweet :id="tweet.id_str"  style="margin-left: 7%"></tweet>

                <h4> {{formatandoScore(tweet.score)}} </h4>
                <img v-if="tweet.score < -0.5" :src="score1" style="width: 65%" class="mb-4">
                <img v-if="tweet.score >= -0.5 && tweet.score < -0.1" :src="score2" style="width: 65%" class="mb-4">
                <img v-if="tweet.score >= -0.1 && tweet.score <= 0.1" :src="score3" style="width: 65%" class="mb-4">
                <img v-if="tweet.score > 0.1   && tweet.score <= 0.5" :src="score4" style="width: 65%" class="mb-4">
                <img v-if="tweet.score > 0.5" :src="score5" style="width: 65%" class="mb-4"> 
              </div>

          </div>
        </v-card>
      </v-flex>

       <!-- Cards de Notícias -->
        <v-flex  sm12 md6 class="pa-3" v-if="scoreNoticia != null">
          <v-card class="px-2">
            <v-card-title><v-icon>language</v-icon><h3 class="ml-3">News</h3></v-card-title>
            <v-divider class="py-2"></v-divider>

            <div class="cardSocial">
              <div v-if="dadosNoticia == ''"  style="text-align: center">
                <h3>Dados Não Encontrados</h3>
              </div>

              <div v-if="scoreNoticia != null" class="py-2 px-4">
                <div v-for="noticia in dadosNoticia" :key="noticia.title" class="divisor text-xs-center mb-3">
                  <h2 class="pa-2">{{noticia.title }} </h2>
                  <a :href="noticia.url"><img :src="noticia.urlToImage" style="width:95%"></a><br><br>
                  <p class="px-4" style="font-size: 17px"> {{ noticia.description }}</p> <br>

                  <h4> {{formatandoScore(noticia.score)}}</h4>
                  <img v-if="noticia.score < -0.5" :src="score1" style="width: 70%">
                  <img v-if="noticia.score >= -0.5 && noticia.score < -0.1" :src="score2" style="width: 70%">
                  <img v-if="noticia.score >= -0.1 && noticia.score <= 0.1" :src="score3" style="width: 70%">
                  <img v-if="noticia.score > 0.1   && noticia.score <= 0.5" :src="score4" style="width: 70%">
                  <img v-if="noticia.score > 0.5" :src="score5" style="width: 70%">
                </div>
              </div>

            </div>
          </v-card>
        </v-flex>

    </v-layout>
  </v-container>

   <v-footer dark height="50" class="grey darken-3 justify-center">
        &copy;2019 — <strong>Multiedro</strong>
  </v-footer>
</div>
</template>

<script>
import axios from "axios";
import { Tweet, Moment, Timeline } from 'vue-tweet-embed'
import * as easings  from 'vuetify/es5/util/easing-patterns'

import imgMtTriste   from '../assets/nivelMtTriste.png'
import imgMeioTriste from '../assets/nivelMeioTriste.png'
import imgNeutro     from '../assets/nivelNeutro.png'
import imgMeioFeliz  from '../assets/nivelMeioFeliz.png'
import imgMtFeliz    from '../assets/nivelMtFeliz.png'

export default {
  components: {
    Tweet
  },

  data() {
    return {
      pesquisa: null,
      termoPesquisado: null,
      carregando: false,
      dadosInstagram: null,
      dadosTwitter: null,
      dadosNoticia: null,
      scoreInstagram: null,
      scoreNoticia: null,
      scoreTwitter: null,
      imgInstagram: null,
      termoNaoEncontrado: false,

      type: 'number',
      number: 500,
      duration: 400,
      offset: 500,
      easing: 'linear',
      easings: Object.keys(easings),
      termoPesquisado: '',
      score1: imgMtTriste,  
      score2: imgMeioTriste,
      score3: imgNeutro,
      score4: imgMeioFeliz,
      score5: imgMtFeliz,

    };
  },

  methods: {

    ativandoScroll(){
      let vm = this
      setTimeout(function () {
               vm.$vuetify.goTo(vm.target, vm.options)
            }, 10);
    },

    async juntandoObjetos(objeto1, objeto2){

        for (let i = 0; i<=objeto1.length; i++){
          Object.defineProperty(objeto1[i], 'score', {
            enumerable: true,
            writable: true,
            value: objeto2[i].score
        })
        }
    },

    formatandoScore(texto){
      return texto.toFixed(2)     
    },

    chamandoDados() {
      this.carregando = true;
      this.dadosInstagram = null;
      this.dadosTwitter = null;
      this.dadosNoticia = null;
      this.scoreInstagram = null;
      this.scoreNoticia = null;
      this.scoreTwitter = null;
      this.imgInstagram = null;
      this.termoPesquisado = this.pesquisa

      axios.all([
        axios.get(`https://newsapi.org/v2/everything?q=${this.pesquisa}&apiKey=e787a0831432407f9925ce02d22703a3&language=pt`),
        axios.get(`https://backend-dot-multiedro-summit.appspot.com/tweets?q=${this.pesquisa}&lang=pt&-filter:links`),
        ]).then(axios.spread((responseNoticias, responseTwitter) => {

          this.dadosTwitter = responseTwitter.data.statuses;
          this.dadosNoticia = responseNoticias.data.articles

        // Pegando os texto para enviar ao back
          let apenasTitulosNoticia = this.dadosNoticia.map(noticia => noticia.title)
          let apenasTextoTwitter = this.dadosTwitter.map(twitter => twitter.full_text)

       //Mandando textos para avaliar o sentimeto na api google
        axios.all([
          axios.post(`https://backend-dot-multiedro-nlp.appspot.com/newsnlp`, apenasTitulosNoticia),
          axios.post(`https://backend-dot-multiedro-nlp.appspot.com/newsnlp`, apenasTextoTwitter),
        ]).then(axios.spread(( responseScoreNoticia, responseScoreTwitter) => {
          this.scoreTwitter = responseScoreTwitter.data
          this.scoreNoticia = responseScoreNoticia.data

          this.juntandoObjetos(this.dadosNoticia, this.scoreNoticia)
          this.juntandoObjetos(this.dadosTwitter, this.scoreTwitter)

          this.pesquisa = null
          this.carregando = false;
          this.ativandoScroll()
          })) // Fechando o .then

          })).catch(function (error) {

          console.log(error.response.status);
          console.log(error);
        })
    },

  },

  computed: {

      target () {
        const value = this[this.type]
         return value
      },

      options () {
        return {
          duration: this.duration,
          offset: this.offset,
          easing: this.easing
        }
      },
  }
};
</script>

<style scoped>

    @import 'https://fonts.googleapis.com/css?family=Roboto:300,400,500,700|Material+Icons';


.cardSocial{
  height:400px;
  overflow-y: scroll;
  overflow-x: hidden;
}

.divisor{
  border: 1px solid rgb(212, 205, 205);
  padding: 10px 8px;
  border-radius: 5px;
  margin-bottom: 7px;
}

.cardSocial::-webkit-scrollbar-track
{
	background-color: #F5F5F5;
}

.cardSocial::-webkit-scrollbar
{
	width: 4px;
	background-color: rgb(226, 224, 224);
}

.cardSocial::-webkit-scrollbar-thumb
{
	background-color: #c5c5c5;
}

.loading-meu-item {
    font-weight: bold;
    font-size: 16px;
    letter-spacing: -1px;
}

.big-header {
  background-image: url('../assets/fundoback.jpg');
  background-size: cover;
  color: white;
  padding: 135px 0px;
}

h1.titulo-page {
    font-size: 74px;
    line-height: 57px;
    font-weight: 900;
}

.desc-page {
  margin-top: 20px;
    font-size: 25px;
    letter-spacing: -1px;
    width: auto;

}

.termo-titulo, .termo-pesquisado {
    width: 100%;
    text-align: center;
}

.termo-titulo {
    font-size: 15px;
    letter-spacing: 0px;
    margin-top: 12px;
    margin-bottom: -7px;
}
.termo-pesquisado {
    font-size: 49px;
    color: #333333;
    margin-top: -6px;
}

span.hashtag i {
    color: black !important;
    font-size: 32px;
    padding-bottom: 11px;
}

</style>
