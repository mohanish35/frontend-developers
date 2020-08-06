
<template>
  <transition name="btn-fade-transition">
    <div
      v-show="btnVisible"
      class="add-btn hidden-md"
    >
      <add-to-cart
        :product="product"
        :disabled="disabled"
      />
    </div>
  </transition>
</template>

<script>
import AddToCart from './AddToCart.vue';
export default {
  components: {
    AddToCart
  },
  props: {
    product: {
      type: Object,
      default: null
    },
    disabled: {
      type: Boolean,
      default: false
    }
  },
  data: () => ({
    btnVisible: true
  }),
  methods: {
    toggleBtnVisibility () {
      this.btnVisible = window.scrollY < 1750
    }
  },
  created () {
    window.addEventListener('scroll', this.toggleBtnVisibility);
  },
  destroyed () {
    window.removeEventListener('scroll', this.toggleBtnVisibility);
  }
}
</script>

<style lang="css" scoped>
  .add-btn {
    position: fixed;
    z-index: 99;
    bottom: 0;
    margin: 20px;
    max-height: 48px;
    width: calc(100% - 100px);
  }

 .btn-fade-transition-enter-active,
  .btn-fade-transition-leave-active {
    transition: opacity 0.7s;
  }

  .btn-fade-transition-enter,
  .btn-fade-transition-leave-to {
    opacity: 0;
  }
</style>
