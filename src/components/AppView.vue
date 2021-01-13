<template>
  <div class="app-view">
    <div class="score-board">
      <img :src="require('../assets/images/logo-bonus.svg')" width="115" height="114" alt="Rock Paper Scissors Lizard Spock" />
      <div class="score-board__score"><h5>Score</h5><p>{{score}}</p></div>
    </div>
    <div class="play-field">
      <img ref="pentagon" class="pentagon" :src="require('../assets/images/bg-pentagon.svg')" />
      <IconDisc :ref="choice" v-for="choice in choices" :key="choice" :icon="choice" @click="$emit('player-select', choice)"/>
      <IconDisc ref="houseChoice" :icon="houseChoice || 'rock'" class="house-choice" />
      <p ref="pickedYou" class="ui-text picked-you">You picked</p>
      <p ref="pickedHouse" class="ui-text picked-house">The house picked</p>
      <p ref="winText" class="ui-text win">{{winText}}</p>
      <CustomButton ref="winButton" :text="buttonText" @click="$emit('again-click')" />
    </div>
    <CustomButton class="rules-button" text="Rules" button-style="secondary" @click="rulesOpen = true"/>
    <p class="creator">Built by Abe Ribeiro | Design by FrontendMentor.com</p>
    <div class="rules-modal" :class="{show: rulesOpen}">
      <div class="rules-modal__panel">
        <div class="rules-modal__header">
          <h2>Rules</h2>
          <img 
            :src="require('../assets/images/iconmonstr-x-mark-1.svg')" 
            alt="Close" 
            class="close-button"
            @click="rulesOpen = false"
          />
        </div>
        <RulesSvg />
      </div>
    </div>
  </div>
</template>

<script>
import IconDisc from './IconDisc'
import CustomButton from './CustomButton'
import RulesSvg from './RulesSvg'
import '../assets/scss/fonts.scss'

const timeout = ms => {
  return new Promise(resolve => setTimeout(resolve, ms));
}

export default {
  components:{
    IconDisc,
    CustomButton,
    RulesSvg
  },
  props:{
    choices: Array,
    state: String,
    playerChoice: String,
    houseChoice: String,
    score: Number
  },
  data(){
    return {
      winText: null,
      buttonText: null,
      rulesOpen: false
    }
  },
  watch: {
    state(){
      switch(this.state){
        case 'started': 
          this.fanOut()
        break;

        case 'playerSelected':
          this.focusSelected()
        break;

        case 'houseSelected':
          this.$nextTick(() => {
             this.revealHouseChoice()
          })
        break;

        case 'tied':
          this.winText = "Its a tie!"
          this.buttonText = "Play again"
          this.revealWinner();
        break;

        case 'playerWin':
          this.winText = "You win!"
          this.buttonText = "Play again"
          this.revealWinner();
        break;

        case 'houseWin':
          this.winText = "You lose!"
          this.buttonText = "Try again"
          this.revealWinner();
        break;

        default:
          this.reset()
        break;

      }
    },
  },
 
  methods:{
    setClass(ref, className, add){
      let el = this.$refs[ref];
      if(el.$el) el = el.$el
      else if(Array.isArray(el)) el = el[0].$el
      if(el) el.classList.toggle(className, add)
    },

    fanOut(){
      for(let i=0; i<this.choices.length; i++){
        const choice = this.choices[i]
        this.setClass(choice, 'show', true)
      }
      this.setClass('pentagon', 'show', true)
    },

    focusSelected(){
      for(let i=0; i<this.choices.length; i++){
        const choice = this.choices[i]

        if(choice === this.playerChoice){
          // Focus on selected
          this.setClass(choice, 'selected', true)

        } else {
          // Hide the rest
          this.setClass(choice, 'show', false)
        }
      }

      this.setClass('pentagon', 'show', false)
      this.setClass('pickedYou', 'show', true)
      
    },

    revealHouseChoice(){
      this.setClass('houseChoice', 'show', true)
      this.setClass('pickedHouse', 'show', true)
    },

    async revealWinner(){
      this.setClass(this.playerChoice, 'spread', true)
      if(this.state === "playerWin") this.setClass(this.playerChoice, 'highlight', true)

      this.setClass('houseChoice', 'spread', true)
      if(this.state === "houseWin") this.setClass('houseChoice', 'highlight', true)

      this.setClass('pickedYou', 'spread', true)
      this.setClass('pickedHouse', 'spread', true)

      await timeout(300) 

      this.setClass('winText', 'show', true)
      this.setClass('winButton', 'show', true)
    },

    reset(){
      this.setClass(this.playerChoice, 'selected', false)
      this.setClass(this.playerChoice, 'spread', false)
      this.setClass(this.playerChoice, 'show', false)
      this.setClass(this.playerChoice, 'highlight', false)
     
      this.setClass('houseChoice', 'spread', false)
      this.setClass('houseChoice', 'show', false)
      this.setClass('houseChoice', 'highlight', false)
      
      this.setClass('pickedYou', 'spread', false)
      this.setClass('pickedYou', 'show', false)
     
      this.setClass('pickedHouse', 'spread', false)
      this.setClass('pickedHouse', 'show', false)
     
      this.setClass('winText', 'show', false)
      this.setClass('winButton', 'show', false)
    }
  }
}
</script>

<style lang="scss">
@import '../assets/scss/colours.scss';
@import '../assets/scss/mixins.scss';

* {
  box-sizing: border-box;
  margin: 0;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  user-select: none;
}

body, button {
  font-family: 'Barlow Semi Condensed', sans-serif;
}

body {
  background: radial-gradient(at top, $blue-darker, $blue-darkest);
  background-attachment: fixed;
}

.creator {
  margin:0;
  position: absolute;
  bottom: 20px;
  left: 20px;
  font-size: 13px;
  color: rgba(white, 0.5);

  @include bp-sm {
    text-align: center;
    width: 100%;
    left: 0;

  }
}

.app-view {
  margin-top: 30px;
  width: 100%;
  overflow: hidden;
}

.score-board {
  border: 3px solid hsl(217, 16%, 45%);
  display: flex;
  justify-content: space-between;
  border-radius: 14px;
  padding: 18px 28px;
  width: 90vw;
  max-width: 680px;
  margin-left: auto;
  margin-right: auto;
  
  @include bp-sm {
    padding: 18px;
  }

  > img {
    display:block;

    @include bp-sm {
      width: 85px;
      height: 85px;
    }
  }

  &__score {
    background: #efefef;
    box-shadow: 0px 3px 4px 0px rgba(0, 0, 0, 0.4);
    border-radius: 8px;
    padding: 16px 0px;
    width: 160px;
    text-align: center;

    h5 {
      color: hsl(229, 64%, 46%);
      text-transform: uppercase;
      font-size: 18px;
      line-height: 18px;
      letter-spacing: 2px;
      font-weight: 600;

      @include bp-sm {
        font-size: 13px;
        line-height: 13px;
      }
    }

    p {
      margin-top: 4px;
      font-size: 64px;
      line-height: 64px;
      color: hsl(229, 25%, 31%);
      font-weight: 700;

      @include bp-sm {
        font-size: 34px;
        line-height: 34px;
      }
    }

    @include bp-sm {
      width: 100px;
      padding: 16px 0px;
    }
  }
}

.rules-button {
  position: fixed;
  bottom: 20px;
  right: 20px;

  @include bp-sm {
    right: auto;
    left: 50%;
    bottom: 60px;
    transform: translate(-50%, 0%);
  }
}

.rules-modal {
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  background: rgba(0, 0, 0, 0.5);

  display: flex;
  align-items: center;
  justify-content: center;

  &__panel {
    background: white;
    padding: 40px;
    border-radius: 10px;
    box-shadow: 3px 3px 4px 0px rgba(0, 0, 0, 0.9);

    max-width: 400px;
    width: 100%;

    svg {
      display: block;
      width: 100%;
    }

    @include bp-sm {
      position: relative;
      border-radius: 0px;
      height: 100vh;
    }
  }

  &__header {
    display: flex;
    align-items: center;
    margin-bottom: 20px;

    @include bp-sm {
      display: block;
      margin-bottom: 40px;
    }

    h2 {
      text-transform: uppercase;
      color: $blue-darker;
      font-size: 28px;
      line-height: 28px;

      @include bp-sm {
        text-align: center;
      }
    }
    
    .close-button {
      margin-left: auto;
      border: 0px;
      background: none;
      padding: 0px;
      color: $blue-darker;
      cursor: pointer;

      transition: transform 150ms ease-in-out;

      &:hover {
        transform: scale(1.1);
      }

      @include bp-sm {
        position: absolute;
        bottom: 40px;
        left: 50%;
        transform: translate(-50%, 0px);
      }
    }

    
  }

  opacity: 0;
  pointer-events: none;
  transition: opacity 300ms ease-in-out;

  &.show {
    opacity: 1;
    pointer-events: all;
  }
}

.play-field {
  position: relative;
  height: 220px;
  margin-top: 250px;
  width: 0px;
  margin-left: auto;
  margin-right: auto;

  @include bp-sm {
    transform: scale(0.8);
    margin-top: 190px;
  }

  .pentagon {
    transition: transform 400ms ease-in-out;
    transform: translate(-50%, -50%) scale(0);

    &.show {
      transform: translate(-50%, -50%) scale(1);
    }
  }

  .icon-disc {    
    transform: translate(0px, 0px) scale(1);

    &--lizard.show {
      transform: translate(-89.8772px, 120.092px);
    }

    &--spock.show {
      transform: translate(-141.988px, -48.3678px);
    }

    &--scissors.show {
      transform: translate(2.12382px, -149.985px);
    }

    &--paper.show {
      transform: translate(143.3px, -44.328px);
    }

    &--rock.show {
      transform: translate(86.4407px, 122.589px);
    }

    cursor: default;
    pointer-events: none;

    &.show {
      opacity: 1;
      cursor: pointer;
      pointer-events: all;
    }

    &.selected {
      cursor: default;
      pointer-events: none;
      transform: translate(-160px, 0px) scale(1.6);

      &.spread {
        transform: translate(-250px, 0px) scale(1.6);
      }

     
    }

    @include bp-sm {
      &.selected {
        transform: translate(-120px, -50px) scale(1.2);
        &.spread {
          transform: translate(-120px, -50px) scale(1.2);
        }
      }
      
    }

    &.highlight {
      /deep/ .blur {
        opacity: 1;
      }
    }
  }

  .icon-disc.house-choice {
    cursor: default;
    pointer-events: none;

    &.show {
      transform: translate(160px, 0px) scale(1.6)
    }

    &.spread {
      transform: translate(250px, 0px) scale(1.6);
    }

    @include bp-sm {
      &.show,
      &.spread {
        transform: translate(120px, -50px) scale(1.2);
      }
    }
  }

  .ui-text {
    position: absolute;
    color: white;
    text-transform: uppercase;
    font-weight: 600;
    font-size: 20px;
    letter-spacing: 2px;
    white-space: nowrap;
    transform: translate(-50%, -50%);
    text-shadow: 0px 2px 2px rgba(0, 0, 0, 0.5);

    transition: all 300ms ease-in-out;
    transition-property: opacity, top, left;

    opacity: 0;
    pointer-events: none;
    &.show {
      opacity: 1;
      pointer-events: all;
    }

    &.picked-you {
      left: -160px;
      top: -135px;

      &.show {
        top: -160px;
      }

      &.spread {
        left: -250px;
      }

      @include bp-sm {
        top: -165px;
        left: -120px;
        &.show,
        &.spread {
          top: -190px;
          left: -120px;
        }
      }
    }

    &.picked-house {
      left: 160px;
      top: -135px;

      &.show {
        top: -160px;
      }

      &.spread {
        left: 250px;
      }

      @include bp-sm {
        top: -165px;
        left: 120px;
        &.show,
        &.spread {
          top: -190px;
          left: 120px;
        }
      }
    }

    &.win {
      top: 0px;
      font-size: 42px;
      letter-spacing: 1px;

      &.show {
        top: -30px;
      }

      @include bp-sm {
        font-size: 50px;
        top: 120px;
        &.show {
          top: 110px;
        }
      }
    }
  }

  .button {
    position: absolute;
    transition: all 300ms ease-in-out;
    transition-property: opacity, top;

    transform: translate(-50%, -50%);

    top: 0px;
    opacity: 0;    
    pointer-events: none;

    &.show {
      opacity: 1;
      pointer-events: all;
      top: 30px;
    }

    @include bp-sm {
      top: 150px;
      &.show {
        top:180px;
      }

      padding: 16px 55px;
      font-size: 22px;
      line-height: 22px;
    }
  }
}
</style>