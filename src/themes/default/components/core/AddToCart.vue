<template>
  <button-full @click.native="addToCart(product)" :class="disableBtn" data-testid="addToCart">
    <div id="btn-content">
      {{ itemAddedToCart ? $t('Added') : $t('Add to cart') }}
      <spinner v-if="showSpinner" />
    </div>
  </button-full>
</template>

<script>
import { formatProductMessages } from '@vue-storefront/core/filters/product-messages'
import { notifications } from '@vue-storefront/core/modules/cart/helpers'
import focusClean from 'theme/components/theme/directives/focusClean'
import ButtonFull from 'theme/components/theme/ButtonFull.vue'
import { mapGetters } from 'vuex'
import Spinner from './Spinner'

export default {
  directives: { focusClean },
  components: { ButtonFull, Spinner },
  props: {
    product: {
      required: true,
      type: Object
    },
    disabled: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      itemAddedToCart: false
    }
  },
  methods: {
    onAfterRemovedVariant () {
      this.$forceUpdate()
    },
    async addToCart (product) {
      try {
        const diffLog = await this.$store.dispatch('cart/addItem', { productToAdd: product })
        this.itemAddedToCart = true;
      } catch (message) {
        this.notifyUser(notifications.createNotification({ type: 'error', message }))
      }
    },
    notifyUser (notificationData) {
      this.$store.dispatch('notification/spawnNotification', notificationData, { root: true })
    }
  },
  computed: {
    ...mapGetters({
      isAddingToCart: 'cart/getIsAdding'
    }),
    showSpinner () {
      return this.disabled || formatProductMessages(this.product.errors) !== '' || this.isAddingToCart
    },
    disableBtn: function () {
      return this.itemAddedToCart && 'disable-btn'
    }
  },
  beforeMount () {
    this.$bus.$on('product-after-removevariant', this.onAfterRemovedVariant)
  },
  beforeDestroy () {
    this.$bus.$off('product-after-removevariant')
  }
}
</script>

<style lang="scss" scoped>
  #btn-content {
      display: inline-flex !important;
  }
  .disable-btn {
    pointer-events: none
  }
</style>
