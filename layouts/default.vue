<template>
  <div :id="id">
    <hc-frame>
      <div class="container" id="main" role="main">
        <hc-verification-banner/>
        <nuxt keep-alive />
      </div>
      <hc-footer/>
    </hc-frame>
  </div>
</template>

<script>
  import HcFooter from '~/components/layout/Footer.vue'
  import HcFrame from '~/components/layout/Frame.vue'
  import HcVerificationBanner from '~/components/layout/VerificationBanner.vue'

  export default {
    components: {
      HcFooter,
      HcFrame,
      HcVerificationBanner
    },
    head () {
      let head = {
        htmlAttrs: {
          lang: this.$i18n.locale()
        }
      }
      if (process.env.NODE_ENV === 'development') {
        head.script = [
          { src: 'https://cdn.jsdelivr.net/npm/tota11y@0.1.6/build/tota11y.min.js' }
        ]
      }
      return head
    },
    computed: {
      id () {
        return `page-name-${this.$route.name}`
      }
    },
    async mounted () {
      // refresh token to fix refresh issues
      await this.$store.dispatch('auth/refreshJWT', 'layout default')
      await this.$store.dispatch('notifications/fetch')
    }
  }
</script>

<style scoped lang="scss">
  #main {
    margin-bottom: 15em !important;
  }
</style>
