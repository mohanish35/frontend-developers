<template>
  <div class="product-quantity">
    <select
      class="quantity-selector"
      v-model="selectedQuantity"
      v-if="!loading && max > 0"
      @change="updateQuantity"
    >
      <option v-for="(iter, id) in max" :key="id" :value="iter">
        {{ iter }}
      </option>
    </select>
    <span v-if="!loading">
      {{ max > 0 ? `${max} Items Available` : "This Item Is Out Of Stock :(" }}
    </span>
    <spinner v-if="loading" />
  </div>
</template>

<script>
import { minValue, maxValue, numeric, required } from 'vuelidate/lib/validators'
import { onlineHelper } from '@vue-storefront/core/helpers'
import Spinner from 'theme/components/core/Spinner'
export default {
  components: {
    Spinner
  },
  props: {
    value: {
      type: [Number, String],
      required: true
    },
    showQuantity: {
      type: Boolean,
      default: false
    },
    maxQuantity: {
      type: Number,
      default: undefined
    },
    checkMaxQuantity: {
      type: Boolean,
      default: true
    },
    loading: {
      type: Boolean,
      default: false
    },
    isSimpleOrConfigurable: {
      type: Boolean,
      default: false
    }
  },
  data: function () {
    return {
      selectedQuantity: this.value
    };
  },
  computed: {
    isOnline (value) {
      return onlineHelper.isOnline
    },
    max () {
      if (!this.isOnline || !this.isSimpleOrConfigurable) {
        return null
      }
      return this.maxQuantity
    },
    disabled () {
      if (!this.isOnline) {
        return false
      }
      return !this.maxQuantity && this.checkMaxQuantity && this.isSimpleOrConfigurable
    },
    name () {
      if (this.isSimpleOrConfigurable && !this.loading && this.showQuantity) {
        return this.$i18n.t(this.isOnline ? 'Quantity available' : 'Quantity available offline', { qty: this.maxQuantity })
      }
      return this.$i18n.t('Quantity')
    }
  },
  validations () {
    return {
      value: {
        minValue: minValue(1),
        maxValue: maxValue(this.max) && !this.isSimpleOrConfigurable,
        numeric,
        required
      }
    }
  },
  watch: {
    '$v.$invalid' (error) {
      this.$emit('error', error)
    }
  },
  methods: {
    updateQuantity () {
      this.$emit('input', this.selectedQuantity)
      this.$v.$touch()
    }
  }
}
</script>
<style lang="scss" scoped>
.quantity-selector {
  width: 46px;
  height: 22px;
  margin-right: 7px;
}
.product-quantity {
  font-size: 18px;
  position: relative;
  /deep/ .spinner {
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    margin: auto;
  }
}
</style>
