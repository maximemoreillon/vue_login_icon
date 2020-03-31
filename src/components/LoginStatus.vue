<template>
  <span class="login_status">


    <transition name="fade" mode="out-in">
      <template v-if="!loading" >

        <!-- MAKE COMPONENT FROM HERE -->
        <template v-if="user">
          <!-- display user avatar if available -->
          <img

            v-if="user.properties.avatar_url"
            v-bind:src="user.properties.avatar_url" >

          <!-- otherwise display generic icon -->
          <account-icon  v-else />

        </template>
        <!-- MAKE COMPONENT TO HERE -->

        <!-- display "not logged in" icon -->
        <account-off-icon v-else=""/>
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


export default {
  name: 'LoginStatus',
  components: {
    Loader,

    // Icons
    AccountIcon,
    AccountOffIcon,
  },
  data(){
    return {
      user: null,
      loading: false,
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
    }
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
