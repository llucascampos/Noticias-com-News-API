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
                label="Digite o nome da empresa"
                v-model="pesquisa"
                color="white"
                class="input-texto"
                dark
                @keyup.enter="chamada(), scroll()"
              ></v-text-field>
              <div style="text-align:right;">
                <v-btn
                @click="chamada(), scroll() "
                color="#7b2ede" dark style="margin-top: 0px !important;"
                :disabled="verificaCampo"
                >Pesquisar</v-btn>
              </div>
          </v-flex>
        </v-layout>
      </v-container>
    </div>

      <v-layout justify-center wrap v-if="termoPesquisado">
        <v-flex xs12>
          <h2 class="termo-titulo">Termo Pesquisado:</h2>
        </v-flex>
        <v-flex xs12>
          <h1 class="termo-pesquisado"><span class="hashtag"><v-icon>search</v-icon></span>{{ termoPesquisado }}</h1>
        </v-flex>
      </v-layout>

      <v-layout justify-center class="my-5" v-if="loadingItem">
          <v-progress-circular
          indeterminate
          color="purple"
          :size="90"
        ><span class="loading-meu-item">Carregando</span>
        </v-progress-circular>
      </v-layout>

<v-container fluid>
      <v-layout wrap row >

        <!-- Cards de Instagram -->
        <v-flex sm12 md4 class="pa-3" v-if="instagram.edges != null">
          <v-card class="px-2">
            <v-card-title><img src="../assets/instagram.png"><h3 class="ml-3">Instagram</h3></v-card-title>
            <v-divider class="py-2"></v-divider>
            <div class="cardSocial">
              <div v-if="instagram.edges == '' || instagram.edges == null "  style="text-align: center">
                <h3>Dados Não Encontrado</h3>
              </div>
              <div v-else v-for="post in instagram.edges" class="divisor" >

                <!-- Aqui o Conteúdo -->
                <img :src="post.node.display_url" style="width: 100%; border-radius: 10px;">
                <div v-for="conteudoPost in post.node.edge_media_to_caption.edges">
                  {{ conteudoPost.node.text }}
                </div>
                <v-divider class="py-1"></v-divider>
              </div>
            </div>
          </v-card>
        </v-flex>

         <!-- Cards de Twitters -->
        <v-flex sm12 md4  class="pa-3"  v-if="twitters != null">
          <v-card class="px-2">
             <v-card-title><img src="../assets/twitter.png"><h3 class="ml-3">Twitter</h3></v-card-title>
            <v-divider class="py-2"></v-divider>
            <div class="cardSocial">
              <div v-if="twitters == ''" style="text-align: center">
                <h3>Dados Não Encontrado</h3>
              </div>
              <div v-else v-for="tweet in twitters" :key="tweet.id">
                <tweet :id="tweet.id_str"></tweet>
              </div>
            </div>
          </v-card>
        </v-flex>

        <!-- Cards de Notícias -->
        <v-flex  sm12 md4 class="pa-3"  v-if="noticias != null" >
          <v-card class="px-2">
             <v-card-title><v-icon>language</v-icon><h3 class="ml-3">News</h3></v-card-title>
            <v-divider class="py-2"></v-divider>
            <div class="cardSocial">
              <div v-if="noticias == ''"  style="text-align: center">
                <h3>Dados Não Encontrado</h3>
              </div>
              <div  v-for="noticia in noticias" class="divisor" >
                <h3>{{noticia.title }} </h3>
                <img :src="noticia.urlToImage" style="width:100%">
                <p> {{ noticia.description }}</p>
                <p> {{ noticia.source.name }}</p>
                <v-divider class="py-1"></v-divider>
              </div>



            </div>
          </v-card>
        </v-flex>
      </v-layout>
      <v-btn @click="enviandoTextos()"> enviar</v-btn>


    {{resultado}}

</v-container>
    <v-footer dark height="auto" style=" text-align: center">
      <div style="width: 100%;text-align: center;">
        &copy;2019 — <strong>Multiedro</strong>
      </div>
    </v-footer>

  </div>
</template>

<script>
import * as easings from 'vuetify/es5/util/easing-patterns'
import axios from 'axios'
import { Tweet, Moment, Timeline } from 'vue-tweet-embed'
export default {
  components:{
    Tweet,
  },

  data(){
    return{
      pesquisa: '',
      titulosNoticia: [],
      instagram: [],
      textoIntagram: [],
      textos: [],
      twitters: null,
      noticias: null,
      loadingItem: false,
      erro: {
        loaded: false,
        msgError: 'Conteúdo não carregado.'
      },
      type: 'number',
      number: 500,
      duration: 400,
      offset: 500,
      easing: 'linear',
      easings: Object.keys(easings),
      termoPesquisado: '',
      resultado: []
    }
  },

  methods:{

    // pegandoTexto(texto, tipo){
    //   if(tipo == "noticias"){
    //     if(texto != null || texto != undefined || texto != ''){
    //       if(this.titulosNoticia.length<= 10){
    //         this.tituloNoticia.push(texto)
    //       }
    //     }
    //   return texto
    //   }

    //   if(tipo == "instagram"){
    //     if(texto != null || texto != undefined || texto != ''){
    //       if(this.textoIntagram.length<= 10){
    //         this.textoIntagram.push(texto)
    //       }
    //     }
    //   return texto
    //   }
    // },

    enviandoTextos(){
      axios.all([
        axios.post(`https://backend-back-dot-multiedro-summit.appspot.com/instanlp`, this.textoIntagram),
        axios.post(`https://backend-back-dot-multiedro-summit.appspot.com/newsnlp`, this.titulosNoticia),
        ]).then(axios.spread((instaRes, resTitulo) => {
          this.resultado = resTitulo.data

          })).catch(function (error) {
          console.log(error.response.status);
        })
    },

    scroll(){
      let vm = this
      setTimeout(function () {
               vm.$vuetify.goTo(vm.target, vm.options)
            }, 10);

    },

    chamada(){
      let vm = this;
      this.loadingItem = true;
      this.erro.loaded = false;
      this.instagram = [];
      this.noticias = null
      this.twitters = null

     // if(this.pesquisa.indexOf(' ') != -1) this.pesquisa = this.pesquisa.split(' ').join('');
      this.termoPesquisado = this.pesquisa

      axios.all([
        axios.get(`https://www.instagram.com/explore/tags/${this.pesquisa}/?__a=1&language=pt`),
        axios.get(`https://newsapi.org/v2/everything?q=${this.pesquisa}&apiKey=e787a0831432407f9925ce02d22703a3&language=pt`),
        axios.get(`https://backend-dot-multiedro-summit.appspot.com/tweets?q=${this.pesquisa}&lang=pt&-filter:links`)
        ]).then(axios.spread((instaRes, newsRes, twitterRes) => {
          this.instagram = instaRes.data.graphql.hashtag.edge_hashtag_to_media
          this.texto = instaRes.data.graphql.hashtag.edge_hashtag_to_media.edges
          this.noticias = newsRes.data.articles
          this.titulosNoticia = this.noticias.map(noticia => noticia.title)

          this.twitters = twitterRes.data.statuses
          this.loadingItem = false;
          this.scroll()

          })).catch(function (error) {
          vm.erro.loaded = true;
          vm.loadingItem = false;
          if(error.response.status == 404) vm.erro.msgError = 'Dado não encontrado, digite novamente sua pesquisa.'
          else vm.erro.msgError = error.response.data
          console.log(error.response.status);
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
      verificaCampo() {
        if(this.pesquisa == null || this.pesquisa == '') return true;
        else return false;
      }
   }
}
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
