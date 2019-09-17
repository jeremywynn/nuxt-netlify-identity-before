<template>
  <div class="container">
    <div>
      <logo />
      <h1 class="title">
        nuxt-netlify-identity
      </h1>
      <h2 class="subtitle">
        My polished Nuxt.js project
      </h2>
      <p v-if="!isLoggedIn">Log in with depi@mail-maker.net : password</p>
      <p>You should only be able to view the Protected Page after logging in. You should be redirected back home if you try to go to the Protected Page and you are not logged in.</p>
      <h3 v-if="isLoggedIn" class="greetings">Hello, authorized user! Go to your <nuxt-link to="/protected">protected page</nuxt-link>.</h3>
      <div class="links">
        <div v-if="isLoggedIn" v-on:click="triggerNetlifyIdentityAction('logout')" class="button--grey">Logout</div>
        <div v-else v-on:click="triggerNetlifyIdentityAction('login')" class="button--grey">Login</div>
        <nuxt-link to="/protected" class="button--green">
          Protected Page
        </nuxt-link>
        <div v-if="!isLoggedIn" v-on:click="circumvent" class="button--green">Circumvent</div>
      </div>
    </div>
  </div>
</template>

<script>
import Logo from '~/components/Logo.vue'
import netlifyIdentity from "netlify-identity-widget";
import { mapActions, mapState } from "vuex";

netlifyIdentity.init();

export default {
  components: {
    Logo
  },
  computed: mapState({
    isLoggedIn: state => state.user.currentUser
  }),
  methods: {
    ...mapActions({
      setUser: 'user/setUser'
    }),
    circumvent() {
      window.localStorage.setItem('user', true);
      location.reload(true);
    },
    triggerNetlifyIdentityAction(action) {
      if (action == "login" || action == "signup") {
        netlifyIdentity.open(action);
        netlifyIdentity.on(action, user => {
          this.setUser(user);
          netlifyIdentity.close();
        });
      } else if (action == "logout") {
        this.setUser(null);
        netlifyIdentity.logout();
        this.$router.push('/');
      }
    },
  }
}
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
  line-height: 1;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.greetings {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  font-weight: 400;
  margin: 1.5rem 0;
}

.links {
  padding-top: 15px;
}

</style>
