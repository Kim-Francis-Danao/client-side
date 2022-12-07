<template>
  <v-form class="mt-16">
    <v-row justify="center">
      <v-col
        sm="3"
      >    
        <v-app-bar 
          absolute
          color="primary"
          dark
          src="../assets/notes.jpg"
          app
        >
          <template v-slot:img="{ props }">
            <v-img
              v-bind="props"
              gradient="to top right, rgba(19,84,122,.5), rgba(128,208,199,.8)"
            />
          </template>

          <v-toolbar-title class="text-h4">
            T O D O
          </v-toolbar-title>
        </v-app-bar>
        
        <div align="center">
          <v-icon 
            class="mt-16" 
            color="black"   
            dense
            x-large
          >
            mdi-lock
          </v-icon>

          <h1>
            LOG IN
          </h1>
        </div>

        <v-text-field
          class="mt-16 pa-3"
          hint="Minimum of 4 characters"
          maxlength="20"
          label="Username"
          v-model="username"
          :rules="[rules.required]"
          @keyup="enableBtn()"
          outlined
        />

        <v-text-field
          class="pa-3"
          hint="Minimum of 4 characters"
          label="Password"
          maxlength="20"
          v-model="password"
          :append-icon="showPass ? 'mdi-eye' : 'mdi-eye-off'"
          :rules="[rules.required]"
          :type="showPass ? 'text' : 'password'"
          @click:append="showPass = !showPass"
          @keyup="enableBtn()"
          outlined
        />

        <v-btn 
          type="submit" 
          class="ml-3" 
          color="primary"
          :disabled="isBtnDisabled" 
          :enabled="isBtnDisabled"
          @click.prevent="submitBtn()"
        >
          Submit
        </v-btn>

        <v-btn
          class="ml-3"
          color="error"
          @click="username = '', password = ''"
        >
          Clear
        </v-btn>
      </v-col>
    </v-row>
  </v-form>
</template>

<script>
  import axios from 'axios';
  
  export default {

    data: () => ({
      username: '',
      password: '',

      isBtnDisabled: true,
      showPass: false,

      rules: {
          required: value => !!value || 'Required.',
          // add another rules...
      },

      userData: []
    }),

    methods: {
      enableBtn() {
        this.username !== ''
        && this.password !== '' 
          ? this.isBtnDisabled = false 
          : this.isBtnDisabled = true;
      },

      async submitBtn() {

        await axios
          .post('http://localhost:1337/api/auth/local', {
            identifier: this.username,
            password: this.password,
          })
          .then(response => {
            // Handle success.
            console.log('User successfuly logged in !!!')
            localStorage.setItem('USER_TOKEN', response.data.jwt);
            location.reload();
          })
          .catch(error => {
            // Handle error.
            alert('Incorrect user details, please try to enter it again.');
            console.log('An error occurred: ', error.response);
          });
        
      }
    }
  }
</script>
