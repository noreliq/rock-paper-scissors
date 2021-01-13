<template>
  <AppView 
    :choices="choices" 
    :state="state" 
    :player-choice="playerChoice"
    :score="score"
    :house-choice="houseChoice"
    @player-select="onPlayerSelect"
    @again-click="reset"
  />
</template>

<script>
import AppView from './components/AppView'

const timeout = ms => {
  return new Promise(resolve => setTimeout(resolve, ms));
}

export default {
  components: {
    AppView,
  },
  data(){
    return {
      state: null,
      playerChoice: null,
      houseChoice: null,
      score: null
    }
  },
  async mounted(){
    this.score = this.savedScore || 0
    await timeout(1000)
    this.state = 'started'
  },
  computed:{
    choices() {
      return ['paper', 'rock', 'lizard', 'spock', 'scissors']
    },
    savedScore(){
      let score = window.localStorage.getItem('score')
      score = Number(score)
      if(!isNaN(score)) return score
      return null
    }
  },
  methods:{
    async onPlayerSelect(choice){     
      if(this.playerChoice) return;

      this.playerChoice = choice;

      // Random house choice
      this.houseChoice = this.choices[Math.floor(Math.random() * this.choices.length)]

      this.state = "playerSelected"      
      await timeout(1000) 
      this.state = 'houseSelected'

      await timeout(1500) 
      this.state = this.getWinState()

      let score = this.score
      if(this.state === "playerWin") score++
      else if(this.state === "houseWin") score--
      this.score = Math.max(0, score)

      window.localStorage.setItem('score', this.score)

    },
    getWinState(){
      const result = ["tied", "playerWin", "houseWin"]

      const playerInd = this.choices.indexOf(this.playerChoice)
      const houseInd = this.choices.indexOf(this.houseChoice)
      let dif = houseInd - playerInd
      if(dif < 0) dif += this.choices.length     
      while(dif > 2) dif -= 2

      return result[dif]
    },
    async reset(){
      this.state = null
      await timeout(800) 

      this.playerChoice = null
      this.houseChoice = null
      this.state = 'started'
    }
  }
}
</script>