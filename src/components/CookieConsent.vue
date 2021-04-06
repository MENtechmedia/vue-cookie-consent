<template>
    <transition appear name="fade">
      <div class="cookie__consent__overlay" v-if="visible && displayCookies !== null">
        <div class="cookie__consent__modal">
          <div>
            <h1>Cookie settings</h1>
            <p>We use cookies, some of them are essential, others are optional. <a :href="cookiePageUrl" target="_blank">Learn more</a></p>
          </div>
          <div v-for="cookie in displayCookies" :key="cookie.cookieName">
            {{ cookie.displayName }} {{ getCookieStatus(cookie) }} <button v-if="cookie.optional" @click="toggleCookie(cookie)">Switch</button>
          </div>
          <div>
            <button @click="hideCookieDialog">Save settings</button>
            <button @click="consentWithAllCookies">Accept all cookies</button>
          </div>
        </div>
      </div>
    </transition>
</template>
<script>
    import collect from 'collect.js';
    import VueCookies from 'vue-cookies';

    export default {
      props: {
        dialogCookieName: String,
        cookiePageUrl: String,
        cookies: Array,
      },

      data() {
        return {
          dialogCookie: {
            cookieName: this.dialogCookieName,
          },
          visible: true,
          cookieValue: 1,
          displayCookies: null,
          renderKey: 0,
        }
      },

      created() {
        if(this.cookieExists(this.dialogCookie)) {
          this.visible = false;
        }
      },

      mounted() {
        this.displayCookies = collect(this.cookies);
        this.displayCookies.where('optional', false).each((cookie) => {
          this.consentWithCookie(cookie);
        });
      },

      methods: {
        cookieExists(cookie) {
          return this.$cookies.isKey(cookie.cookieName);
        },

        getCookieStatus(cookie) {
          if(this.cookieExists(cookie)) {
            return true;
          }

          return cookie.initialStatus;
        },

        consentWithAllCookies() {
          collect(this.cookies).each((cookie) => this.consentWithCookie(cookie));
          this.hideCookieDialog();
        },

        consentWithCookie(cookie) {
          if(!this.cookieExists(cookie)) {
            this.setCookie(cookie.cookieName, this.cookieValue, 365);
          }
          this.forceRenderer();
        },

        forceRenderer() {
          this.renderKey++;
          this.$forceUpdate();
        },

        hideCookieDialog() {
          this.visible = false;
          const date = new Date();
          // Remember dialog box for 365 days
          date.setTime(date.getTime() + (365 * 24 * 60 * 60 * 1000));
          this.$cookies.set(this.dialogCookie.cookieName, 1, date.toUTCString());
          location.reload();
        },

        removeCookie(name) {
          this.$cookies.remove(name);
        },

        retractCookieConsent(cookie) {
          if(this.cookieExists(cookie) && cookie.optional === true) {
            this.removeCookie(cookie.cookieName);
          }
          this.forceRenderer();
        },

        setCookie(name, value, expirationInDays) {
          const date = new Date();
          date.setTime(date.getTime() + (expirationInDays * 24 * 60 * 60 * 1000));
          this.$cookies.set(name, value, date.toUTCString());
        },

        toggleCookie(cookie) {
          if(cookie.optional === true) {
            let cookieExists = this.cookieExists(cookie);

            if(cookieExists === false) {
              this.consentWithCookie(cookie);
            }

            if(cookieExists === true) {
              this.retractCookieConsent(cookie);
            }
          }
        }
      }
    }
</script>
<style lang="scss">
  body {
    margin: 0 auto;
  }

  .cookie__consent__overlay {
    top: 0;
    left: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    position: fixed;
    height: 100%;
    width: 100%;
    background: rgba(0,0,0,0.5);
  }

  .cookie__consent__modal {
    background: white;
    width: 50%;
  }
</style>
