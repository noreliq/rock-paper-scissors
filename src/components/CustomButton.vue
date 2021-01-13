<template>
  <button :class="`button button--${buttonStyle}`" v-on="$listeners">{{text}}</button>
</template>

<script>
export default {
  props:{
    text:String,
    buttonStyle:{
      type: String,
      default: 'primary'
    }
  }
}
</script>

<style lang="scss" scoped>
  .button {
    border: 0;
    outline: 0;
    background: none;
    border-radius: 8px;
    text-transform: uppercase;
    white-space: nowrap;
    
    cursor: pointer;
    
    font-size: 18px;
    line-height: 18px;
    letter-spacing: 2px;
    font-weight: 600;

    transition: all 120ms ease-in-out;
    transition-property: color, background-color, border-color, box-shadow;

    &--primary {
      border: 2px solid transparent;
      background: #efefef;
      box-shadow: 0px 3px 4px 0px rgba(0, 0, 0, 0.4);
      padding: 12px 50px;
      color: hsl(229, 25%, 31%);

      &:hover {
        background: transparent;
        color: #efefef;
        border-color: #efefef;
        box-shadow: none;
      }
    }

    &--secondary {
      border: 2px solid #efefef;
      color: #efefef;
      padding: 10px 26px;

      &:hover {
        background: #efefef;
        color: hsl(229, 25%, 31%);
        box-shadow: 0px 3px 4px 0px rgba(0, 0, 0, 0.4);
      }

    }
  }
</style>