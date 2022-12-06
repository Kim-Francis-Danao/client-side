<template>
  <v-list
    class="pt-0"
    flat
  > 
    <v-text-field
      class="pa-3"
      append-icon="mdi-plus-circle"
      label="Add Task"
      outlined
      v-model="newTask"
      @click:append="addTask"
      @keyup.enter="addTask"
    />

    <div
      v-for="task in userData.tasks"
      :key="task.id"
    > 
      <v-list-item
        @click="doneTask(task.id)"
        :class="{'grey lighten-2' : task.isDone}"
      >
        <template v-slot:default>
          <v-list-item-action>
            <v-checkbox color="green" :input-value="task.isDone" />
          </v-list-item-action>

          <v-list-item-content>
            <v-list-item-title
              :class="{'text-decoration-line-through' : task.isDone}"
            >
              {{ task.taskName }}
            </v-list-item-title>
          </v-list-item-content>

          <v-list-item-action>
            <v-btn 
              @click.stop="deleteTask(task.id)"
              icon
            >
              <v-icon color="black">mdi-trash-can</v-icon>
            </v-btn>
          </v-list-item-action>
        </template>
      </v-list-item>
    </div>
  </v-list>
</template>

<script>
  import axios from 'axios';

  export default {
    name: 'Home',

    props: {
      token: { type:String }
    },

    data: () => ({  
      error: null,
      newTask: '',
      config: {},
      userData: []
    }),

    mounted() {
      this.getData(); 
    },

    methods: {
      async getData() {
        try {
          this.config = {
            headers: {
              Authorization: `Bearer ${this.token}`
            }
          }

          const response = await axios.get('http://localhost:1337/api/users/me?populate=*', this.config)

          this.userData = response.data;
          console.log('Get user data successful!!!', this.userData);
        } catch (error) {
          this.error = error;
        }
      },
      
      async addTask() {
        const bodyParameters = {
          "data" : {
            taskName: this.newTask,
            isDone: false,
            users_permissions_user: this.userData.id
          }
        };

        await axios
        .post(
          'http://localhost:1337/api/tasks',
          bodyParameters,
          this.config
        )
        .then( response => {
          console.log('New task is added! ', response);
          this.getData();
        })
        .catch(error => {
          console.log('An error occurred:', error);
        });

        this.newTask = '';
      },

      async doneTask(id) {
        const task = this.userData.tasks.filter(task => task.id === id)[0];

        if(task.isDone) {
          await axios
            .put(`http://localhost:1337/api/tasks/${id}`, {
              "data": {
                isDone: false
              }
            }, this.config)
            .then( response => {
              console.log('Task changed into not done! ', response);
              this.getData();
            })
            .catch(error => {
              console.log('An error occurred:', error);
            });
        } else {
          await axios
            .put(`http://localhost:1337/api/tasks/${id}`, {
              "data": {
                isDone: true
              }
            }, this.config)
            .then( response => {
              console.log('Task changed into done! ', response);
              this.getData();
            })
            .catch(error => {
              console.log('An error occurred:', error);
          });
        }
      },

      async deleteTask(id) {
        await axios
          .delete(`http://localhost:1337/api/tasks/${id}`, this.config)
          .then( response => {
            console.log('Task has been deleted! ', response);
            this.getData();
          })
          .catch(error => {
            console.log('An error occurred:', error);
        });
      }
    }
  }
</script>
