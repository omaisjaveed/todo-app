<template>
    <div class="container mt-5">
        <div class="row justify-content-center">
            <div class="col-md-4">
                <div class="card">
                    <div class="card-header">Todo</div>

                    <div class="card-body">
                        <div class="input-group">
                            <input type="text" placeholder="todo" v-model="todo_input" class="form-control"
                            />
                            <div class="input-group-append">
                                <button v-if="!edit_todo_id" type="button" class="btn btn-info" @click="save_todo_data()">Add</button>
                                <button v-else type="button" class="btn btn-warning" @click="update_todo()">Update</button>
                            </div>
                        </div>


                        <div>
                            <table class="table table-bordered mt-5">
                                <thead>
                                    <th>S.No</th>
                                    <th>Name</th>
                                    <th>Action</th>
                                </thead>

                                <tbody>
                                
                                <tr v-for="(todo,index) in  todos" :key="(index)">
                                    <td>{{ ++ index}}</td>
                                    <td>{{todo.name}}</td>
                                    <td>
                                        <button type="button" class="btn btn-danger" @click="delete_data(todo.id)">Delete</button>
                                        <button type="button" class="btn btn-info" @click="editTodo(todo.id)">Edit</button>
                                    </td>
                                    
                                </tr>

                                </tbody>
                            </table>
                        </div>


                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {

        data() {
            return {
                todos:[],
                api: 'http://localhost:8000/api/todos',
                todo_input : '',
                edit_todo_id : ''
            }
        },


        mounted() {
            console.log('Component mounted.')
            this.axios.get(this.api).then(res => {
                this.todos = res.data;
            });
        },

        methods: {
            save_todo_data(){
                if(this.todo_input.length > 0){
                    var data = {'name' : this.todo_input} 

                    this.axios.post(this.api, data).then(res => {
                        this.todos.push(res.data);
                        this.todo_input = ''
                    });

                }
            },

            delete_data(id){
                if(id){
                    this.axios.delete(this.api + '/' + id).then(res => {
                        console.log('ressss',res)
                        const index = this.todos.findIndex(todo => todo.id === id);
                        if (index !== -1) {
                            // Remove the item from the todos array
                            this.todos.splice(index, 1  );
                        }
                            
                    });
                }
            },
            editTodo(id){
                if (id) {
                    const todo = this.todos.find(todo => todo.id === id); // Find the todo with the matching id
                    console.log('todo',todo)

                    if (todo) {
                        this.todo_input = todo.name; // Set the name to the input field
                        this.edit_todo_id = todo.id;
                    } 
                }

            },

            update_todo(){

                if(this.todo_input.length > 0){
                    var data = {'name' : this.todo_input} 

                    this.axios.patch(this.api+ '/' + this.edit_todo_id, data).then(res => {
                         // Assuming the response returns the updated todo item (or at least the name)
                        const updatedTodo = res.data;

                        // Find the index of the todo item in the array
                        const index = this.todos.findIndex(todo => todo.id === this.edit_todo_id);
                        console.log('indexxx ', index)

                        if (index !== -1) {
                            // Update the todo item in the array
                            this.todos[index].name = updatedTodo.name; // You can add other fields if needed
                        }
                    });

                }

            }

            
        },
    }
</script>
