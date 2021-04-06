<template>
  <div id="app">
      <cookie-consent dialog-cookie-name="cookie-consent-cookie-dialog-box" cookie-page-url="https://youtube.com" :cookies="[
          {
            displayName: 'Technical',
            cookieName: 'technical',
            description: 'Technical cookies...',
            optional: false,
            initialStatus: true,
          },
          {
            displayName: 'Functional',
            cookieName: 'functional',
            description: 'Functional cookies...',
            optional: true,
            initialStatus: false,
          },
          {
            displayName: 'Analytical',
            cookieName: 'analytical',
            description: 'Analytical cookies...',
            optional: true,
            initialStatus: false,
          },
          {
            displayName: 'Commercial',
            cookieName: 'commercial',
            description: 'Commercial cookies...',
            optional: true,
            initialStatus: false,
          },
      ]"></cookie-consent>
  </div>
</template>

<script>
import CookieConsent from './components/CookieConsent'
export default {
  name: 'app',
  components: {
    CookieConsent
  },
  data () {
    return {

    }
  }
}
</script>