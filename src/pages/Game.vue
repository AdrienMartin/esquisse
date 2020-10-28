<template>
  <div class="alignCenter bleu">
    <p class="tour">Tour {{numeroTour}}</p>

    <p>{{ordreJoueurs.join(' ➔ ')}}</p>

    <div v-if="typeTour==0">
      <img :src="dessinADeviner" class="tailleImage borderDessin marginRight"/>
      <div style="margin-top:1%">
      <b-form-input v-model="motDevine" placeholder="alors ?" class="inputMot marginRight" :disabled="motValide"/>
      <b-button variant="dark" v-on:click="validerMot()" v-if="!motValide">Valider</b-button> 
      </div>
      <p v-if="motValide">En attente de joueurs !</p>
    </div>
    
    <div v-if="typeTour==1">
      <p>{{motADessiner}}</p>
      <p v-if="timerCount>0">{{timerCount}}</p>
      <p v-if="timerCount<=0">0</p>
      <Dessin ref="dessin"/>
    </div>

    <div v-if="numeroTour==1">
      <p v-if="timerCount>0">{{timerCount}}</p>
      <p v-if="timerCount<=0">0</p>
      <p v-if="numeroTour==1">Ton mot est {{motADessiner}}</p>
      <p v-if="numeroTour==1 && ordreJoueurs.length%2==0">Attention : tu vas devoir le dessiner</p>
    </div>
  </div>
</template>

<script>
import Dessin from "./Dessin";

export default {
  components: {
    Dessin
  },
  props: {
    ordreJoueurs: {
      default: []
    },
    game: {},
    indexPlayer: {}
  },
  data() {
    return {
      numeroTour : 1,
      timerCount : 0, // changer la valeur de timerCount pour démarrer le chrono
      typeTour: 0, // 0 = mot, 1 = dessin
      dessinADeviner: "",
      motADessiner: "",
      motDevine: "",
      nbJoueurs : this.ordreJoueurs.length,
      motValide: false
    };
  },
  methods: {
    validerMot() {
      this.motValide = true;
      this.$socket.emit('sendDrawOrWord', this.motDevine);
      this.motDevine="";
    }
  },
  
  sockets: {
    endGame (data) {
      console.log("fin du jeu");
      this.$router.push({ name: 'EndGame',  params: {game : data, ordreJoueurs: this.ordreJoueurs} });
    },
    newTurn(data) {
      // determination du type de tour en fonction du numero de tour
      this.motValide = false;
      this.numeroTour++;
      this.typeTour = data.typeTour;
      if (this.numeroTour%2 == 1) {
        // pair = mot
        this.dessinADeviner = data.dessin;
      } else {
        //impair = dessin
        this.motADessiner = data.mot;
        this.timerCount = 80;
      }
    },
    firstTurn(data) {
      this.numeroTour = 1;
      this.typeTour = data.typeTour;
      this.motADessiner = data.mot;
      this.timerCount = 20;
    }
  },
  watch: {
    timerCount: {
        handler(value) {
            if (value > 0) {
                setTimeout(() => {
                    this.timerCount--;
                }, 1000);
            } else if ((value == 0 ||value == -1)){
              this.timerCount = -2;
              // fin du tour de dessin
              if (this.numeroTour == 1) {
                this.$socket.emit('endFirstTurn');
              } else {
                this.$refs.dessin.saveImage();
              }
            }
        },
        immediate: true
    }
  },
  created() {
    this.$socket.emit('firstTurn');
  }
};

</script>

<style>
  .bleu {
    color : rgb(21,39,125);
  }
  .alignCenter {
    text-align: center;
  }

  .inputMot {
    display: initial;
    width: 12%
  }
  .borderDessin {
    margin-top : 10px;
    border : 1px solid rgba(0,0,0,.13);
  }

  .marginRight {
    margin-right : 1%;
  }
  .tour {
    font-weight : bold;
    font-size: larger;
  }
</style>
