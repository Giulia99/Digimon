<template>
  <div class="md-layout md-gutter"> 
    <md-dialog-alert
      :md-active.sync="alert"
      md-content="Hai già raggiunto il numero massimo di 5 Digimon per squadra!"
      md-confirm-text="Chiudi"/>

    <div class="md-layout-item md-size-25 md-medium-size-33 md-small-size-50 md-xsmall-size-100"
      v-for="Digimon in Digimons"
      :key="Digimon.name"
      @click="DigimonInfo(Digimon.name)"> 
      
      <md-card md-with-hover>
        <md-card-media>
          <img :src="Digimon.img" />
        </md-card-media>

        <md-card-header>
          <div class="md-title">{{ Digimon.name }}</div>
          <div class="md-subhead">{{ Digimon.level }}</div>
        </md-card-header>

          <md-card-actions>       
              <md-button class="md-icon-button" @click="like(Digimon.name)" onclick="event.stopPropagation()">
                <md-icon v-if="likes.includes(Digimon.name)">favorite</md-icon>
                <md-icon v-else>favorite_border</md-icon>
                <md-tooltip md-direction="bottom">Preferiti</md-tooltip>
              </md-button>
            
              <md-button class="md-icon-button" @click="team(Digimon.name)" onclick="event.stopPropagation()">
                <md-icon v-if="teams.includes(Digimon.name)">playlist_add_check</md-icon>
                <md-icon v-else>playlist_add</md-icon>
                <md-tooltip md-direction="bottom">Squadra</md-tooltip>
              </md-button>    
          </md-card-actions>      
        
      </md-card>
    </div>
  </div>
</template>

<script>
import dataservice from "../dataservice";
export default {
  data: function () {
    return {
      Digimons: [],
      likes: [],
      teams: [],
      alert: false,
    };
  },
  created: function () {
    dataservice.getDigimon().then((data) => { 
      this.Digimons = data.data; 
    });
    this.controllike();
    this.controlteam();
  },
  methods: {
    DigimonInfo(name) { 
      this.$router.push({ path: "/info/" + name });
    },
    //funzione che toglie elementi da array likes e teams 
    remove(arr, value) { 
      var i = 0; 
      while (i < arr.length) {
        if (arr[i] === value) {
          arr.splice(i, 1);
        } else {
          ++i;
        }
      }
    return arr; 
    },
    like(name){
      if (this.likes.includes(name)) { 
        dataservice.removelike(name)
        this.likes = this.remove(this.likes,name)
      } else {
        dataservice.like(name)
        this.likes.push(name)
      }
    },
    controllike() {  
      this.likes = []; 
      dataservice.controllike().then(data => {
        this.likes = data; 
      })
    },
    team(name){
      if (this.teams.includes(name)) {
        dataservice.removeteam(name)
        this.teams = this.remove(this.teams,name)
      } else {
        if (this.teams.length === 5) {
          this.alert=true;
        } else {
          dataservice.team(name)
          this.teams.push(name)
        } 
      }
    },
    controlteam() {
      this.teams = [];
      dataservice.controlteam().then(data => {
        this.teams = data;
      })
    }
  }
}

</script>

<style>
.md-card {
  margin: 10px;
}
</style>