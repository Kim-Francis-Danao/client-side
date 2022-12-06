<template>
  <v-app 
    id="inspire" 
  >
    <login-user 
      v-if="!userIsLogin"
      @userIsLogin="logIn($event)" 
    />

    <v-navigation-drawer 
      v-model="drawer"
      app
      v-if="userIsLogin"
    >
      <v-list-item>
        <v-list-item-content>
          <v-list-item-title class="text-h6">
            To-do-list Application
          </v-list-item-title>
          <v-list-item-subtitle>
            By: Kim Francis
          </v-list-item-subtitle>
        </v-list-item-content>
      </v-list-item>

      <v-divider></v-divider>

      <v-list
        dense
        nav
      >
        <v-list-item
          v-for="item in items"
          :key="item.title"
          :to="item.to"
          @click="item.logOut ? userIsLogin = false : '' "
          link
        >
          <v-list-item-icon>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-icon>

          <v-list-item-content>
            <v-list-item-title>{{ item.title }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-app-bar 
      absolute
      color="primary"
      dark
      src="./assets/notes.jpg"
      prominent
      app
      v-if="userIsLogin"
    >
      <template v-slot:img="{ props }">
        <v-img
          v-bind="props"
          gradient="to top right, rgba(19,84,122,.5), rgba(128,208,199,.8)"
        ></v-img>
      </template>

      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>

      <v-toolbar-title>T O D O</v-toolbar-title>

      <v-spacer></v-spacer>

      <v-btn icon>
        <v-icon>mdi-magnify</v-icon>
      </v-btn>
    </v-app-bar>

    <v-main v-if="userIsLogin">
      <router-view :token="token"/>
    </v-main>
  </v-app>
</template>

<script>
  import LoginUser from './components/LoginUser.vue'

  export default {
  components: { LoginUser },

    data: () => ({ 
      token: '',

      drawer: null,
      right: null,

      userIsLogin: false,

      items: [
        { 
          title: 'T O D O - List', 
          icon: 'mdi-clipboard-list', 
          to: '/',
          logOut: false
        },

        { 
          title: 'About', 
          icon: 'mdi-information',
          to: '/about',
          logOut: false
        },

        { 
          title: 'Log Out', 
          icon: 'mdi-logout',
          logOut: true
        },
      ],
    }),

    methods: {

      logIn(userData) {
        try {
          this.userIsLogin = userData[0].userIsLogin;
          this.token = userData[0].token;
          console.log('Entering todo app...');
        } catch (error) {
          console.log('An error occurred: ', error);
        }
      },

      logOut() {
        userIsLogin = false
      }
    }
  }
</script>