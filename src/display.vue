<template>
  <span class="no-wrap">{{ items }}</span>
</template>

<script>
import mixin from '@directus/extension-toolkit/mixins/interface'

export default {
  mixins: [mixin],
  computed: {
    items () {
      return this.value.map(item => `"${item.title}"`).join(', ')
    }
  }
}
</script>
