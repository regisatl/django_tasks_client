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
                              <input type="text" class="form-control" id="title" v-model="title"
                                    placeholder="Entrez un titre...">
                        </div>
                        <div class="form-group">
                              <label for="description">Description</label>
                              <textarea class="form-control" id="description" v-model="description"
                                    placeholder="Entrez une description..."></textarea>
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
                              <h3>{{ task.title }}</h3>
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

h1,
h2 {
      text-align: center;
      color: midnightblue;
}

.tasks_list {
      list-style: none;
      padding: 0;
}

.tasks_list li {
      background-color: #fff;
      border-radius: 0.5rem;
      box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
      padding: 2rem;
      margin-bottom: 1.5rem;
}

.tasks_list h3 {
      font-size: 1.25rem;
      font-weight: 700;
      margin-bottom: 1rem;
}

.tasks_list p {
      margin-bottom: 1.5rem;
      color: #4a5568;
}

.tasks_list button {
      padding: 0.5rem 1rem;
      border-radius: 0.25rem;
      border: none;
      cursor: pointer;
      font-weight: medium;
}

.tasks_list button:first-of-type {
      background-color: #48bb78;
      color: #fff;
      margin-right: 1rem;
}
.tasks_list button:first-of-type:hover{
      background-color: #23b861;
}
.tasks_list button:last-of-type:hover {
      background-color: #e02424;
}

.tasks_list button:last-of-type {
      background-color: #e53e3e;
      color: #fff;
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
      color: midnightblue;
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
      font-weight: medium;
}

.form-group button:hover {
      background-color: #2374f7;
}
</style>