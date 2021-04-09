<template>
    <renderless-cookie-consent v-model="cookies" dialog-cookie-name="certscanner_cookie">
        <div slot-scope="{ cookies, toggleCookie, getCookieStatus, visible, consentWithAllCookies, retractAllCookies, hideCookieDialog }">
          <div v-for="cookie in cookies" :key="cookie.cookieName" v-if="visible">
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
          <div>
            <button @click="hideCookieDialog">Close</button>
            <button @click="retractAllCookies">Retract all</button>
            <button @click="consentWithAllCookies">Accept all</button>
          </div>
        </div>
    </renderless-cookie-consent>

</template>
<script>
    import RenderlessCookieConsent from "./RenderlessCookieConsent";

    export default {
      components: {RenderlessCookieConsent},
      props: {
        jsonCookies: {
          type: Array,
        },
      },

      data() {
        return {
          cookies: this.jsonCookies,
        }
      },
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
