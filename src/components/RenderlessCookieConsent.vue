<script>
  import collect from 'collect.js';
  import * as Cookie from 'tiny-cookie';

  export default {
    model: {
      prop: 'cookies',
    },

    props: {
      cookies: {
        type: Array,
      },
      dialogCookieName: {
        type: String,
        default: 'cookie_consent'
      },
    },

    data() {
      return {
        visible: true,
        cookieValue: 1,
        displayCookies: null,
        renderKey: 0,
      }
    },

    mounted() {
        collect(this.cookies).where('optional', false).each((cookie) => {
          this.consentWithCookie(cookie);
        });
    },

    methods: {
      cookieExists(cookieName) {
        return Cookie.get(cookieName) !== null;
      },

      getCookieStatus(cookie) {
        if(this.cookieExists(cookie.cookieName)) {
          return true;
        }

        return cookie.initialStatus;
      },

      consentWithAllCookies() {
        collect(this.cookies).each((cookie) => this.consentWithCookie(cookie));
        this.hideCookieDialog();
      },

      consentWithCookie(cookie) {
        if(!this.cookieExists(cookie.cookieName)) {
          this.setCookie(cookie.cookieName, this.cookieValue);
        }
        this.forceRenderer();
      },

      forceRenderer() {
        this.renderKey++;
        this.$forceUpdate();
      },

      hideCookieDialog() {
        this.visible = false;
        this.setCookie(this.dialogCookieName, 1)
        location.reload();
      },

      removeCookie(name) {
        Cookie.removeCookie(name);
      },

      retractAllCookies() {
        collect(this.cookies).each((cookie) => this.retractCookieConsent(cookie));
        this.hideCookieDialog();
      },

      retractCookieConsent(cookie) {
        if(this.cookieExists(cookie.cookieName) && cookie.optional === true) {
          this.removeCookie(cookie.cookieName);
        }
        this.forceRenderer();
      },

      setCookie(name, value, expiresInDays) {
        Cookie.setCookie(name, value, { expires: '1Y' });
      },

      toggleCookie(cookie) {
        if(cookie.optional === true) {
          let cookieExists = this.cookieExists(cookie.cookieName);

          if(cookieExists === false) {
            this.consentWithCookie(cookie);
          }

          if(cookieExists === true) {
            this.retractCookieConsent(cookie);
          }
        }
      }
    },

    render() {
      return this.$scopedSlots.default({
        cookies: this.cookies,
        cookieExists: this.cookieExists,
        consentWithAllCookies: this.consentWithAllCookies,
        getCookieStatus: this.getCookieStatus,
        hideCookieDialog: this.hideCookieDialog,
        toggleCookie: this.toggleCookie,
        visible: this.visible,
        retractAllCookies: this.retractAllCookies,
      })
    }
  }
</script>