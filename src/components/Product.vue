<template>
  <div class="product">

    <!-- product thumbnails -->
    <div class="thumbs">
      <div class="wrapper" v-for="(image, index) of product.images" v-bind:key="index">
        <img :src="image" @mouseover="showImage(index)">
      </div>
    </div>

    <!-- product image -->
    <div class="image">
      <img
        v-for="(image, index) of product.images"
        v-bind:key="index"
        v-bind:src="image"
        v-bind:class="{ active: index === 0 }">
    </div>

    <div class="content">

      <!-- product name -->
      <div class="name">
        {{ product.name }}

        <!-- favorite action -->
        <a
          class="like button"
          v-bind:class="{ 'is-favorite': product.isFavorite }"
          @click="$emit('toggleFavorite')"
        >
          <i class="like icon"></i>
        </a>
      </div>

      <!-- product tags -->
      <div class="ui tags">
        <slot name="tags"></slot>
      </div>

      <div class="two columns">
        <div class="column">

          <!-- product price -->
          <div class="installments">{{ product.price.installments }}x R$ <strong>{{ product.price.installmentValue | currency('') }}</strong></div>
          <div class="cash">ou <em>{{ product.price.value | currency }}</em> à vista</div>

        </div><div class="column">

          <!-- buy button -->
          <button class="ui buy button" v-on:click="$emit('addToCart')">Adicionar ao carrinho</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
$padding:                  7px;
$thumbnail-size:           50px;
$thumbs-size:              $thumbnail-size + (2 * $padding);
$thumb-image-size:         40px;
$image-size:               300px + (2 * $padding);
$content-margin-left:      ($thumbs-size + $image-size);
$vertical-elements-gutter: 15px;

.product {
  background-color: white;
  position: relative;
  border: none;
  border-radius: 3px;
  font-size: 1rem;
  padding: 20px $padding 20px 0;
  box-shadow: 2px 3px 8px rgba(0, 0, 0, .25);
  min-height: 200px;
}

.thumbs {
  box-sizing: border-box;
  position: absolute;
  top: 0;
  left: 0;
  width: $thumbs-size;
  height: 100%;
  padding: $padding;
  overflow: hidden;
  text-align: center;

  .wrapper {
    border: 1px solid #eeeeee;
    border-radius: 3px;
    margin-bottom: 2px;
    padding: 2px;
  }

  img {
    display: block;
    margin: 0 auto;
    height: $thumb-image-size;
    max-width: $thumb-image-size;
  }
}

.image {
  box-sizing: border-box;
  position: absolute;
  top: 0;
  left: $thumbs-size;
  width: $image-size;
  height: 100%;
  padding: $padding;

  img {
    display: none;
    margin: 0 auto;
    height: 200px;
    max-width: 200px;

    &.active {
      display: block;
    }
  }
}

.content { margin-left: $content-margin-left }

.name {
  color: #444444;
  font-size: 1.7em;
  border-bottom: 1px solid #cccccc;
  padding-bottom: $vertical-elements-gutter;
  margin-bottom: $vertical-elements-gutter;
}

.ui.tags:empty { display: none }

.two.columns {
  padding-top: $vertical-elements-gutter;

  .ui.tags:empty + & { padding-top: 39px }

  .column {
    box-sizing: border-box;
    display: inline-block;
    vertical-align: top;
    width: 50%;
    height: 100%;
    margin: 0;
    padding: 0 10px;

    @media screen and (max-width: 836px) {
      display: block;
      width: 100%;
      text-align: center;
    }
  }
}

.two.columns .column + .column {
  @media screen and (max-width: 836px) {
    margin-top: 20px
  }
}

.installments {
  color: #00b151;
  font-size: 1.15rem;
  margin-bottom: 10px;

  strong { font-size: 1.4em }
}

.cash {
  color: #757575;

  em {
    color: #00b151;
    font-style: normal;
  }
}

.like.button {
  cursor: pointer;
  color: #ffcd25;
  padding-left: 10px;
}

.like.icon:before {
  display: inline-block;
  font: normal normal normal 14px/1 FontAwesome;
  font-size: 0.7em;
  text-rendering: auto;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  vertical-align: middle;

  content: '\f08a'
}

.like.button.is-favorite .like.icon:before,
.like.icon:hover:before
{ content: '\f004' }

.buy.button {
  width: 100%;
  padding: 7px 3px;
  font-family: 'Montserrat', sans-serif;
  font-weight: 700;
  background-color: #00b151;
  color: white;
  border: 0;
}

@media only screen and (min-width : 768px) {}
</style>

<script>
export default {
  name: 'product',

  methods: {
    showImage (index) {
      let images = this.$el.querySelectorAll('.image img')

      for (let image of images) image.classList.remove('active')

      images[index].classList.add('active')
    }
  },
  props: [ 'product' ]
}
</script>
