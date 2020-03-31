<template>
  <span
    class="login_status"
    v-on:mouseover="mouseover()"
    v-on:mouseleave="mouseleave()">


    <transition name="fade" mode="out-in">
      <template v-if="!loading" >

        <!-- MAKE COMPONENT FROM HERE -->
        <template v-if="user">

          <template v-if="!hover">
            <!-- display user avatar if available -->
            <img
              class="status_icon"
              v-if="user.properties.avatar_url"
              v-bind:src="user.properties.avatar_url" >

            <!-- otherwise display generic icon -->
            <account-icon
              v-else
              class="status_icon" />
          </template>

          <template v-else>

            <logout-icon
              class="control_icon"
              v-on:click="logout()"/>
          </template>

        </template>


        <!-- MAKE COMPONENT TO HERE -->

        <!-- display "not logged in" icon -->
        <template v-else>
          <account-off-icon
            v-if="!hover"
            class="status_icon" />

          <login-icon
            v-else
            class="control_icon"
            v-on:click="login()"/>
        </template>



      </template>

      <Loader v-if="loading"/>
    </transition>

  </span>
</template>

<script>

import axios from 'axios'
import VueCookies from 'vue-cookies'
import Loader from '@moreillon/vue_loader'

import AccountIcon from 'vue-material-design-icons/Account.vue';
import AccountOffIcon from 'vue-material-design-icons/AccountOff.vue';
import LoginIcon from 'vue-material-design-icons/Login.vue';
import LogoutIcon from 'vue-material-design-icons/Logout.vue';


export default {
  name: 'LoginStatus',
  components: {
    Loader,

    // Icons
    AccountIcon,
    AccountOffIcon,
    LoginIcon,
    LogoutIcon,
  },
  data(){
    return {
      user: null,
      loading: false,
      hover: false,
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
        axios.post('https://authentication.maximemoreillon.com/whoami', {},
        { headers: { Authorization : `Bearer ${jwt}`} })
        .then(response => { this.user = response.data})
        .catch( () => { /* Nothing */ })
        .finally( () => {
          this.loading = false
          this.$emit('loadingFinished')

        })

      }
    },
    mouseover(){
      this.hover = true;
    },
    mouseleave(){
      this.hover = false;
    },
    logout(){
      VueCookies.remove('jwt')
      // Not very elegant way to deal with this
      location.reload()
    },
    login(){
      location.href = "https://authentication.maximemoreillon.com/"
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
/* Not scoped because need to affect icons */
.login_status{
  display: inline-flex;
  align-items: center;

  cursor: pointer;
}


.login_status img {
  height: 1em;
  width: 1em;
}

.login_status .material-design-icon {
  height: 1em;
  width: 1em;
}

.material-design-icon > .material-design-icon__svg {
  height: 1em;
  width: 1em;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity .25s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}


</style>
