<template>
  <a
    :href="authenticationFrontUrl"
    class="login_status">

    <!-- transition from loading to not loading -->
    <transition
      name="fade"
      mode="out-in">

      <alert-circle-outline-icon
        v-if="error || !authenticationApiUrl || !authenticationFrontUrl"/>

      <Loader
        v-else-if="loading"
        class="loader"/>



      <!-- If not loading or errored -->
      <template v-else >

        <template v-if="user">

          <!-- display user avatar if available -->
          <img
            class="avatar"
            v-if="user.properties.avatar_src"
            v-bind:src="user.properties.avatar_src" >

          <!-- otherwise display generic icon -->
          <account-icon
            v-else
            class="status_icon" />

        </template>

        <!-- display "not logged in" icon -->
        <account-off-icon
          v-else
          class="status_icon" />


      </template>

    </transition>

  </a>
</template>

<script>

import axios from 'axios'
import VueCookies from 'vue-cookies'
import Loader from '@moreillon/vue_loader'

import AccountIcon from 'vue-material-design-icons/Account.vue'
import AccountOffIcon from 'vue-material-design-icons/AccountOff.vue'
import AlertCircleOutlineIcon from 'vue-material-design-icons/AlertCircleOutline.vue'


export default {
  name: 'LoginStatus',
  components: {
    Loader,

    // Icons
    AccountIcon,
    AccountOffIcon,
    AlertCircleOutlineIcon,
  },
  props: {
    authenticationApiUrl: {
      type: String,
      default: process.env.VUE_APP_AUTHENTICATION_API_URL,
    },
    authenticationFrontUrl: {
      type: String,
      default: process.env.VUE_APP_AUTHENTICATION_FRONT_URL,
    },
  },
  data(){
    return {
      user: null,
      loading: false,
      hover: false,
      error: false,

    }
  },
  mounted(){
    this.check_login_status()
  },
  methods: {
    check_login_status(){

      let jwt = VueCookies.get('jwt')

      if(jwt) {
        this.loading = true;
        axios.post(`${this.authenticationApiUrl}/whoami`, {},
        { headers: { Authorization : `Bearer ${jwt}`} })
        .then(response => {
          this.user = response.data
        })
        .catch( () => {
          this.error = true
        })
        .finally( () => {
          this.loading = false
          this.$emit('loadingFinished')

        })
      }
    },

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
/* Not scoped because need to affect icons */
.login_status{
  display: inline-flex;
  align-items: center;
  cursor: pointer;
  text-decoration: none;
  color: currentColor;
}


.avatar, .material-design-icon__svg {
  height: 1em;
  width: 1em;
}




.fade-enter-active, .fade-leave-active {
  transition: opacity .25s;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
}


</style>
