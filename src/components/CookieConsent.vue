<template>
    <transition appear name="fade">
      <div class="cookie__consent__overlay" v-if="visible">
        <div class="cookie__consent__modal">
          <div class="cookie__consent__title_box">
            <p class="cookie__consent__title_text">{{ modalTitleText }}</p>
            <p>{{ modalDescriptionText }} <a v-if="cookiePageUrl != null" :href="cookiePageUrl" target="_blank">{{ cookiePageUrlText }}</a></p>
          </div>
          <div class="cookie_consent__cookie_box">
            <div v-for="cookie in displayCookies" :key="cookie.cookieName">
              <div class="cookie__consent__switches" @click="toggleCookie(cookie)">
                <div class="cookie__consent__switches__cookie_name">
                  <p>{{ cookie.displayName }}</p>
                </div>
                <div v-if="cookie.optional">
                  <input type="checkbox" :checked="getCookieStatus(cookie) ? 'checked' : null" class="cookie__consent__switch-input">
                  <label class="cookie__consent__switch-label"></label>
                </div>
                <div v-else>
                    -
                </div>
              </div>
            </div>
          </div>
          <div class="cookie__consent__buttons">
            <button class="cookie__consent__hide_dialog_button" @click="hideCookieDialog">{{ closeButtonText }}</button>
            <button class="cookie__consent__accept_all_button" @click="consentWithAllCookies">{{ acceptCookiesText }}</button>
          </div>
        </div>
      </div>
    </transition>
</template>
<script>
    import collect from 'collect.js';
    import * as Cookie from 'tiny-cookie';

    export default {
      props: {
        acceptCookiesText: {
          type: String,
          default: "Accept all cookies"
        },
        closeButtonText: {
          type: String,
          default: "Save settings"
        },
        cookiePageUrl: {
          type: String,
          required: false
        },
        cookiePageUrlText: {
          type: String,
          default: "Learn more"
        },
        modalTitleText: {
          type: String,
          default: "Cookie settings"
        },
        modalDescriptionText: {
          type: String,
          default: "We use cookies, some of them are essential, others are optional.",
        },
        dialogCookieName: {
          type: String,
        },
        cookies: {
          type: Array,
        }
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
          return Cookie.get(cookie.cookieName) !== null;
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
          this.setCookie(this.dialogCookie.cookieName, 1)
          location.reload();
        },

        removeCookie(name) {
          Cookie.removeCookie(name);
        },

        retractCookieConsent(cookie) {
          if(this.cookieExists(cookie) && cookie.optional === true) {
            this.removeCookie(cookie.cookieName);
          }
          this.forceRenderer();
        },

        setCookie(name, value, expiresInDays) {
          Cookie.setCookie(name, value, { expires: '1Y' });
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
  /**
    Setting
   */
  body {
    margin: 0 auto;
  }

  /**
    Overlay & modal
   */
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
    z-index: 999999;
  }

  .cookie__consent__modal {
    background: white;
    width: 100%;
    max-width: 500px;
  }

  .cookie__consent__title_box {
    padding: 20px 40px;
  }

  .cookie__consent__title_text {
    font-size: 18px;
    font-weight: bold;
  }

  .cookie__consent__title_box > p > a {
    color: #FF991F;
    text-decoration: none;
  }

  .cookie_consent__cookie_box {
    background-color: #F4F5F7;
    padding: 20px 40px;
  }

  /**
    Buttons
   */
  .cookie__consent__buttons {
    margin: 32px 0;
    padding: 0 40px 0;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .cookie__consent__buttons > button{
    transition-property: all;
    transition-duration: 300ms;
    cursor: pointer;
    border-radius: 0.25rem;
    font-weight: bold;
    border: 1px solid;
    padding: 0.75rem 1.5rem;
    margin: 0 4px 0;
    width: 50%;
  }

  .cookie__consent__hide_dialog_button {
    background: transparent;
    border-color: red;
    color: gray;
  }

  .cookie__consent__hide_dialog_button:hover {
    background-color: rgba(244, 245, 247, 1);
  }

  .cookie__consent__accept_all_button {
    color: white;
    background: linear-gradient(135deg, #ffc400 0%, #ff8b00 73%, #ff8b00 100%);
  }

    /**
      Switch toggles
     */
  .cookie__consent__switches {
    width: 100%;
    display: inline-flex;
    align-items: center;
    height: 50px;
  }

  .cookie__consent__switches__cookie_name {
    width: 85%;
  }

  .cookie__consent__switches > div > p {
    font-weight: bold;
    width: 100px;
  }

  .cookie__consent__switch-input {
    display: none;
  }
  .cookie__consent__switch-label {
    position: relative;
    display: inline-block;
    cursor: pointer;
    font-weight: 500;
    text-align: left;

  }
  .cookie__consent__switch-label:before, .cookie__consent__switch-label:after {
    content: "";
    position: absolute;
    margin: 0;
    outline: 0;
    top: 50%;
    -ms-transform: translate(0, -50%);
    -webkit-transform: translate(0, -50%);
    transform: translate(0, -50%);
    -webkit-transition: all 0.3s ease;
    transition: all 0.3s ease;
  }
  .cookie__consent__switch-label:before {
    background: #F4F5F7;
    left: 1px;
    width: 57px;
    height: 32px;
    box-shadow: 2px 2px 11px 0px rgba(0,0,0,0.03) inset;
    border-radius: 200px;
  }
  .cookie__consent__switch-label:after {
    background: white;
    left: 5px;
    width: 25px;
    height: 25px;
    border-radius: 50%;
    box-shadow: 0 4px 6px -1px rgba(0, 0, 0, .1), 0 2px 4px -1px rgba(0, 0, 0, .06);
  }
  .cookie__consent__switch-label .cookie__consent__toggle--on {
    display: none;
  }
  .cookie__consent__switch-label .cookie__consent__toggle--off {
    display: inline-block;
  }
  .cookie__consent__switch-input:checked + .cookie__consent__switch-label:before {
    background: #36B37E;
  }
  .cookie__consent__switch-input:checked + .cookie__consent__switch-label:after {
    background: white;
    -ms-transform: translate(100%, -50%);
    -webkit-transform: translate(100%, -50%);
    transform: translate(100%, -50%);
  }
  .cookie__consent__switch-input:checked + .cookie__consent__switch-label .cookie__consent__toggle--on {
    display: inline-block;
  }
  .cookie__consent__switch-input:checked + .cookie__consent__switch-label .cookie__consent__toggle--off {
    display: none;
  }
</style>
