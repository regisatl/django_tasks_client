<script>
export default {
      name: 'TaskList', // Utiliser un nom en plusieurs mots
      data() {
            return {
                  // tasks
                  tasks: [''],
                  title: '',
                  description: ''
            }
      },
      methods: {
            async getData() {
                  try {
                        // fetch tasks
                        const response = await this.$http.get('http://localhost:8000/api/tasks/');
                        // set the data returned as tasks
                        this.tasks = response.data;
                  } catch (error) {
                        // log the error
                        console.log(error);
                  }
            },
            async submitForm() {
                  try {
                        // Send a POST request to the API
                        const response = await this.$http.post('http://localhost:8000/api/tasks/', {
                              title: this.title,
                              description: this.description,
                              completed: false
                        });
                        // Append the returned data to the tasks array
                        this.tasks.push(response.data);
                        // Reset the title and description field values.
                        this.title = '';
                        this.description = '';
                  } catch (error) {
                        // Log the error
                        console.log(error);
                  }
            },
            async toggleTask(task) {
                  try {

                        // Send a request to API to update the task
                        const response = await this.$http.put(`http://localhost:8000/api/tasks/${task.id}/`, {
                              completed: !task.completed,
                              title: task.title,
                              description: task.description
                        });

                        // Get the index of the task being updated
                        let taskIndex = this.tasks.findIndex(t => t.id === task.id);

                        // Reset the tasks array with the new data of the updated task

                        this.tasks = this.tasks.map((task) => {
                              if (this.tasks.findIndex(t => t.id === task.id) === taskIndex) {
                                    return response.data;
                              }
                              return task;
                        });

                  } catch (error) {

                        // Log any error
                        console.log(error);
                  }
            },
            async deleteTask(task) {

                  // Confirm if one wants to delete the task
                  let confirmation = confirm("Do you want to delete this task?");

                  if (confirmation) {
                        try {

                              // Send a request to delete the task
                              await this.$http.delete(`http://localhost:8000/api/tasks/${task.id}`);

                              // Refresh the tasks
                              this.getData();
                        } catch (error) {

                              // Log any error

                              console.log(error)
                        }
                  }
            }
      },
      created() {
            // Fetch tasks on page load
            this.getData();
      }
}
</script>

<template>
      <div class="tasks_container">
            <div class="add_task">
                  <h1>Add a task</h1>
                  <form v-on:submit.prevent="submitForm">
                        <div class="form-group">
                              <label for="title">Title</label>
                              <input type="text" class="form-control" id="title" v-model="title" placeholder="Entrez un titre...">
                        </div>
                        <div class="form-group">
                              <label for="description">Description</label>
                              <textarea class="form-control" id="description" v-model="description" placeholder="Entrez une description..."></textarea>
                        </div>
                        <div class="form-group">
                              <button type="submit">Add Task</button>
                        </div>
                  </form>
            </div>
            <div class="tasks_content">
                  <h2>Tasks</h2>
                  <ul class="tasks_list text-xl font-medium text-black">
                        <li v-for="task in tasks" :key="task.id">
                              <h2>{{ task.title }}</h2>
                              <p>{{ task.description }}</p>
                              <button @click="toggleTask(task)">
                                    {{ task.completed ? 'Undo' : 'Complete' }}
                              </button>
                              <button @click="deleteTask(task)">Delete</button>
                        </li>
                  </ul>
            </div>
      </div>
</template>

<style>
body {
      font-family: Poppins;
      max-width: 800px;
      margin: 0 auto;
}

h1 {
      text-align: center;
      color: midnightblue;
}

.form-group {
      margin-bottom: 1rem;
}

.form-group label {
      display: block;
      margin-bottom: 0.5rem;
      font-weight: 600;
}

.form-group input[type="text"],
.form-group textarea {
      display: block;
      width: 100%;
      padding: 0.8rem;
      border: 1px solid #d1d5db;
      border-radius: 0.5rem;
}

.form-group button[type="submit"] {
      display: inline-block;
      padding: 0.5rem 1rem;
      color: #fff;
      background-color: #3b82f6;
      border: none;
      border-radius: 0.25rem;
      cursor: pointer;
}
</style>