<template>
  <button
    type="button"
    class="bg-cl-transparent brdr-none inline-flex"
    @click="openSidebarMenu"
    :aria-label="$t('Open menu')"
    data-testid="menuButton"
  >
    <i
      :class="'material-icons ' + (sidebarActive ? 'rotate' : '')"
    >
      {{ sidebarActive ? "arrow_forward" : "dehaze" }}
    </i>
  </button>
</template>

<script>
import HamburgerIcon from '@vue-storefront/core/compatibility/components/blocks/Header/HamburgerIcon'
import { mapGetters } from 'vuex'

export default {
  mixins: [HamburgerIcon],
  computed: {
    ...mapGetters({
      sidebarActive: 'ui/getSidebarActive'
    })
  }
}
</script>

<style scoped>
  i {
    transition: transform 0.3s;
  }
  i.rotate {
    transform: rotate(180deg);
  }
  button:hover > i{
    transform: scale(1.5, 0.1);
  }
</style>
