<template>
  <button-full @click.native="addToCart(product)" :disabled="isProductDisabled" :data-testid="testid" id="add-to-cart-btn">
    {{ $t('Add to cart') }}
  </button-full>
</template>

<script>
import { formatProductMessages } from '@vue-storefront/core/filters/product-messages'
import { notifications } from '@vue-storefront/core/modules/cart/helpers'
import focusClean from 'theme/components/theme/directives/focusClean'
import ButtonFull from 'theme/components/theme/ButtonFull.vue'
import { mapGetters } from 'vuex'

export default {
  directives: { focusClean },
  components: { ButtonFull },
  props: {
    product: {
      required: true,
      type: Object
    },
    disabled: {
      type: Boolean,
      default: false
    },
    testid: {
      type: String,
      default: 'addToCart'
    }
  },
  methods: {
    onAfterRemovedVariant () {
      this.$forceUpdate()
    },
    async addToCart (product) {
      try {
        const diffLog = await this.$store.dispatch('cart/addItem', { productToAdd: product })
        diffLog.clientNotifications.forEach(notificationData => {
          this.notifyUser(notificationData)
        })
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
    isProductDisabled () {
      return this.disabled || formatProductMessages(this.product.errors) !== '' || this.isAddingToCart
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
  #add-to-cart-btn{
    @media (max-width: 767px) {
        height: 48px !important;
        padding: 0 !important;
    }
  }
</style>
