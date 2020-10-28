<template>
  <div class="centrer bleu">
    <div v-if="!isEnregistrer && !partieEnCours" class="marginTop5">
      <b-form-input v-model="nomJoueur" placeholder="Ton pseudo" class="pseudo" />
      <b-button variant="dark" v-on:click="enregister()" class="marginLeft10">Valider </b-button>
    </div>
    <div v-if="isEnregistrer" class="marginTop5">
      <p>Connecté. En attente du début de partie.</p>
      <b-button variant="dark" v-if="joueursConnectes.length>3 && joueursConnectes.length<9" v-on:click="debuterPartie()">Commencer</b-button>
    </div>
    <p v-if="!partieEnCours" class="marginTop" >Nombre de joueurs dans la partie : {{joueursConnectes.length}}</p>
    <p v-if="!partieEnCours && joueursConnectes.length>0" class="marginTop">Liste des joueurs connectés : {{joueursConnectes.join(', ')}}</p>
    <div>
      <p v-for='event in events' :key='event._id'>{{event.name}} vient de {{event.action}} la partie. </p>
    </div>
    <p v-if="partieEnCours" class="marginTop5">Une partie est déjà en cours.</p>
  </div>  
</template>

<script>

  export default {
    data() {
        return {
          isConnected: false,
          nomJoueur: '',
          joueursConnectes: [],
          events: [],
          isEnregistrer : false,
          indexPlayer: '',
          partieEnCours: false
        }
    },
     
    sockets: {
      connect() {
        console.log("connect");
        this.isConnected = true;
      },
      disconnect() {
        this.isConnected = false;
      },
      playerConnection(data) {
        this.events.push({name:data.username, action:'rejoindre'});
      },
      getPlayers(players) {
        this.joueursConnectes = players;
        console.log("joueursConnectes : " + this.joueursConnectes );
      },
      partieEnCours(enCours){
        this.partieEnCours = enCours;
      },
      playerDisconnection(data) {
        this.events.push({name:data.name, action:'quitter'});
      },
      startGame(data) {
        this.$router.push({ name: 'Game',  params: { game: data.game, indexPlayer: this.indexPlayer, ordreJoueurs : data.ordreJoueurs}});
      },
      indexPlayer(index) {
        this.indexPlayer = index;
      }
    },
    methods: {
        enregister() {
         this.$socket.emit('register', this.nomJoueur);
         this.isEnregistrer = true;
        },
        debuterPartie() {
          this.myTurn = true;
          this.$socket.emit('startGame');
        }
    },
    created() {
      this.$socket.emit('getPlayers');
    }
  }
  
</script>

<style>
  .centrer {
      text-align: center;
  }
  .marginTop {
    margin-top: 20px;
  }
  .marginTop5 {
    margin-top : 5%;
  }
  .marginLeft10 {
    margin-left: 10px;
  }
  .bleu {
    color : rgb(21,39,125);
  }
  .pseudo {
    display: initial;
    width: 20%
  }
</style>
