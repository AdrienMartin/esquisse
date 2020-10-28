<template>
  <div class="bleu"> 
     <p style="text-align: center;">{{ordreJoueurs.join(' ➔ ')}}</p>
      <div v-for='livret in game.livrets' :key='livret._id' class="row justify-content-md-center" style="border-bottom : solid; margin-top:2%; height:250px">
        <p class="centrerVerticalement">Livret de {{livret.proprietaire }} : </p>
        <div v-for='page in livret.pages' :key='page._id' class="centrerVerticalement" style="margin-left:5%">
          <div>
            <p v-if="page.typePage==0" style="margin-bottom:0px">{{page.contenu}}</p>
            <img v-if="page.typePage==1" :src="page.contenu" class="image borderDessin" style="margin-bottom:10px;" />    
          </div>     
        </div>
      </div>
      <div class="row justify-content-md-center marginTop">
        <b-button variant="dark"  v-on:click="newGame()">Nouvelle partie</b-button>
      </div>
  </div>
</template>

<script>

  export default {
    components: {
    },
    props:  ['game', 'ordreJoueurs'],
    data() {
        return {
        }
    },
    methods: {
      newGame() {
        this.$router.push({ name: 'Home' });
      }
    },
    created() {
    }
    
  }
  
</script>

<style>
  .centrer {
      text-align: center;
  }

  .imageBigBen {
    height:200px;
    width: auto;   
  }

  .marginTop{
    margin-top : 1%;
  }

  .bleu {
    color : rgb(21,39,125);
  }

  .image {
    height : 200px;
  }

  .centrerVerticalement {
    margin-top: auto;
    margin-bottom: auto;
  }
.borderDessin {
    border : 1px solid rgba(0,0,0,.13);
  }

</style>
