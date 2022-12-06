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
      v-if="!isUpdating"
      v-model="newTask"
      @click:append="addTask"
      @keyup.enter="addTask"
    />
    
    <v-text-field
      class="pa-3"
      append-icon="mdi-circle-edit-outline"
      label="Upate Task"
      outlined
      v-if="isUpdating"
      v-model="newTask"
      @click:append="updateTask"
      @keyup.enter="updateTask"
    />
    <div align="end" class="mt-3">
      <span class="text-h7 mr-5">Delete All:</span>
      <v-btn
        class="mr-3"
        color="primary"
        elevation="2"
        small
        @click="delFinished"
      > 
        Finished
      </v-btn>
      <v-btn
        class="mr-3"
        color="primary"
        elevation="2"
        small
        @click="delUnfinish"
      >
        Unfinish
      </v-btn>
    </div>

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
              @click.stop="udatingTask(task.id, task.taskName)"
              icon
            >
              <v-icon color="black">mdi-pencil</v-icon>
            </v-btn>
          </v-list-item-action>

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
      updateId: null,
      newTask: '',
      config: {},
      userData: [],
      isUpdating: false
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
          console.log('An error occurred:', error);
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

      udatingTask(id, taskName) {
        this.newTask = taskName;
        this.updateId = id;
        this.isUpdating = true;
      },

      async updateTask() {        
        const bodyParameters = {
          "data" : {
            taskName: this.newTask,
            isDone: false,
            users_permissions_user: this.userData.id
          }
        };
        await axios
        .put(`http://localhost:1337/api/tasks/${this.updateId}`, 
          bodyParameters, 
          this.config
        )
        .then( response => {
          console.log('Task has been updated! ', response);
          this.getData();
        })
        .catch(error => {
          console.log('An error occurred:', error);
        });

        this.newTask = '';
        this.isUpdating = false;
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
      },

      async delFinished() {
        await axios
          .delete(`http://localhost:1337/api/delCompleteTask`, this.config)
          .then( response => { 
            console.log('delete completed task successful!!! ', response)
            this.getData();          
          })
          .catch(error => {
            console.log('An error occurred:', error);
          })
          ;
      },

      async delUnfinish() {
        await axios
          .delete(`http://localhost:1337/api/delIncompleteTask`, this.config)
          .then( response => { 
            console.log('delete incomplete task successful!!! ', response)
            this.getData();          
          })
          .catch(error => {
            console.log('An error occurred:', error);
          })
          ;
      },

    }
  }
</script>
