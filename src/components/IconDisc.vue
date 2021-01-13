<template>
  <div :class="`icon-disc icon-disc--${icon}`" v-on="$listeners">
    <div class="inner">
      <img :src="require(`../assets/images/icon-${icon}.svg`)" :alt="icon">
    </div>
    <div class="blur"></div>
  </div>
</template>

<script>
export default {
  props:['icon'],
}
</script>

<style lang="scss" scoped>
  @import '../assets/scss/colours.scss';

  .icon-disc {
    transition: all 400ms ease-in-out;
    transition-property: transform, opacity;
    opacity: 0;
    position: absolute;
    left: -70px;
    top: -70px;

    .inner {
      width: 140px;
      height: 140px;
      border-radius: 200px;

      // Inset shadow, plus the regular shadow
      box-shadow: inset 0px -6px 0px 0px rgba(50, 50, 50, 0.35),
                0px 3px 4px 0px rgba(0, 0, 0, 0.5);

      // White layer with inset shadow             
      &::after {
        content: '';
        position: absolute;
        transform: translate(-50%, -50%);
        left: 50%;
        top: 50%;
        width: 110px;
        height: 110px;
        border-radius: 200px;
        background: #efefef;
        box-shadow: inset 0px 6px 0px 0px rgba(50, 50, 50, 0.1);

        transition: all 200ms ease-out;
        transition-property: width, height;
      }

      img {
        z-index: 1;
        position: absolute;
        transform: translate(-50%, -50%);
        left: 50%;
        top: 50%;
        transition: transform 200ms ease-out;
      }
    }
    
    &--lizard {
      .inner { background: linear-gradient($purple, darken($purple, 4%)); }
    }

    &--scissors {
      .inner { background: linear-gradient($yellow, darken($yellow, 4%)); }
    }

    &--rock {
      .inner { background: linear-gradient($pink, darken($pink, 4%)); }
    }

    &--paper {
      .inner { background: linear-gradient($blue,  darken($blue, 4%)); }
    }

    &--spock {
      .inner { background: linear-gradient($cyan, darken($cyan, 4%)); }
    }

    .blur {
      content: '';
      width: 140px;
      height: 140px;
      border-radius: 140px;
      position: absolute;
      top: 0;
      left: 0;
      z-index: -1;

      background: rgba(255, 255, 255, 0.3);
      transform: scale(1.2);
      filter: blur(30px);

      opacity: 0;
      transition: opacity 300ms ease-out;
    }

    &:hover {
      .inner::after {
        width: 120px;
        height: 120px;
      }
      
      img {
        transform: translate(-50%, -50%) scale(1.2);
      }
    }
    
  }
</style>