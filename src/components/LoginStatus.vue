<template>
  <a
    :href="authentication_front_url"
    class="login_status">

    <!-- transition from loading to not loading -->
    <transition
      name="fade"
      mode="out-in">

      <!-- if loading -->
      <Loader
        v-if="loading"
        class="loader"/>

      <alert-circle-outline-icon
        v-else-if="error"/>

      <!-- If not loading or errored -->
      <template v-else >

        <template v-if="user">

          <!-- display user avatar if available -->
          <img
            class="avatar"
            v-if="user.properties.avatar_url"
            v-bind:src="user.properties.avatar_url" >

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
  data(){
    return {
      user: null,
      loading: false,
      hover: false,
      error: false,
      authentication_api_url: 'https://api.authentication.maximemoreillon.com/whoami',
      authentication_front_url: 'https://authentication.maximemoreillon.com/',
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
        axios.post(`${this.authentication_api_url}`, {},
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
