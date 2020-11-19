<template>
    <v-main class="list">
        <h3 class="text-h3 font-weight-medium mb-5">To Do List TGS</h3>
        
        <v-card style="margin: 2vh;" outlined elevation="2">
            <v-card-title>
                <v-text-field
                    v-model="search"
                    append-icon="mdi-magnify"
                    label="Search"
                    single-line
                    hide-details
                ></v-text-field>
                <v-spacer></v-spacer>
                <v-btn color="purple" dark @click="dialogSelesai = true" style="margin: 0 1vh 0 0">
                    TODO SELESAI
                </v-btn>
                <v-btn color="success" dark @click="dialog = true"  style="margin: 0 0 0 1vh">
                    Tambah
                </v-btn>
            </v-card-title>
            
            <v-data-table :headers="headers" :items="todos" :search="search">
                 <template v-slot:[`item.priority`]="{ item }">
                    <v-chip v-if="item.priority == 'Penting'"
                        class="ma-2"
                        color="red"
                        label
                        outlined>
                        {{ item.priority }}
                    </v-chip>
                    <v-chip v-else-if="item.priority == 'Biasa'"
                        class="ma-2"
                        color="blue"
                        label
                        outlined>
                        {{ item.priority }}
                    </v-chip>
                    <v-chip v-else
                        class="ma-2"
                        color="green"
                        label
                        outlined>
                        {{ item.priority }}
                    </v-chip>
                </template>

            <template v-slot:[`item.actions`]="{ item }">
                <v-icon class="mr-2 blue--text" @click="editItem(item)">
                    edit
                </v-icon>
                <v-icon class="red--text" @click="dialogDelete = true; temp = item">
                    delete
                </v-icon>
            </template>

            <template v-slot:[`item.check`]="{ item }">
                <v-checkbox @change="checkItem(item)" v-bind:value="item.check"> <!-- v-bind:value for unchecked when do deleteSelected() -->
                </v-checkbox>
            </template>
            </v-data-table>
        </v-card>
        
        <v-card v-if="isWantDel() == true" style="margin: 2vh;" outlined elevation="2">
            <v-card-text class="text--primary">
                <div>Delete Multiple</div>
            </v-card-text>
            <ul v-for="(todo, index) in todos" :key="index">
                <li v-if="todo.check == true">{{ todo.task }}</li>
            </ul>
            <v-btn color="error" depressed @click="deleteSelected()" style="margin: 1vh">
                HAPUS SEMUA
            </v-btn>
        </v-card>

        <v-dialog v-model="dialog" persistent max-width="600px">
            <v-card>
                <v-card-title>
                    <span class="headline">Form Todo</span>
                </v-card-title>
                <v-card-text>
                    <v-container>
                        <v-text-field
                            v-model="formTodo.task"
                            label="Task"
                            required
                        ></v-text-field>

                        <v-select
                            v-model="formTodo.priority"
                            :items="['Penting', 'Biasa', 'Tidak penting']"
                            label="Priority"
                            required
                        ></v-select>

                        <v-textarea
                            v-model="formTodo.note"
                            label="Note"
                            required
                        ></v-textarea>
                    </v-container>
                </v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="cancel">
                        Cancel
                    </v-btn>
                    <v-btn color="blue darken-1" text @click="save">
                        Save
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

        <v-dialog v-model="dialogDelete" persistent max-width="400px">
            <v-card>
                <v-card-title>
                <span class="headline"><b>Yakin menghapus?</b></span>
                </v-card-title>
                <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="green darken-1" text @click="cancelDelete">
                    TIDAK
                </v-btn>
                <v-btn color="red darken-1" text @click="deleteItem(temp)">
                    YA
                </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>

        <v-dialog v-model="dialogSelesai" persistent max-width="600px">
            <v-data-table :headers="headersSelesai" :items="todosSelesai" :search="search">
                 <template v-slot:[`item.priority`]="{ item }">
                    <v-chip v-if="item.priority == 'Penting'"
                        class="ma-2"
                        color="red"
                        label
                        outlined>
                        {{ item.priority }}
                    </v-chip>
                    <v-chip v-else-if="item.priority == 'Biasa'"
                        class="ma-2"
                        color="blue"
                        label
                        outlined>
                        {{ item.priority }}
                    </v-chip>
                    <v-chip v-else
                        class="ma-2"
                        color="green"
                        label
                        outlined>
                        {{ item.priority }}
                    </v-chip>
                </template>
            </v-data-table>

            <v-card>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="blue darken-1" text @click="close">
                        Close
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </v-main>
</template>
<script>
export default {
    name: "List",
    data() {
        return {
            search: null,
            dialog: false,
            dialogDelete: false,
            dialogSelesai: false,
            headers: [
                {
                    text: "Task",
                    align: "start",
                    sortable: true,
                    value: "task",
                },
                { text: "Priority", value: "priority" },
                { text: "Note", value: "note" },
                { text: "Actions", value: "actions" },
                { text: "Select", value: "check" },
            ],
            headersSelesai: [
                {
                    text: "Task",
                    align: "start",
                    sortable: true,
                    value: "task",
                },
                { text: "Priority", value: "priority" },
                { text: "Note", value: "note" },
            ],
            todos: [
                {
                    task: "bernafas",
                    priority: "Penting",
                    note: "huffttt",
                    check: false,
                },
                {
                    task: "nongkrong",
                    priority: "Tidak penting",
                    note: "bersama tman2",
                    check: false,
                },
                {
                    task: "masak",
                    priority: "Biasa",
                    note: "masak air 500ml",
                    check: false,
                },
            ],
            formTodo: {
                task: null,
                priority: null,
                note: null,
                check: false, //set default value for check
            },
            todosSelesai: [
                
            ],
        };
    },
    methods: {
        save() {
            let i;
            for(i = 0; i < this.todos.length; i++) {
                if(this.todos[i].task==this.formTodo.task && this.todos[i].priority==this.formTodo.priority && this.todos[i].note==this.formTodo.note) {
                    this.todos[i].task = this.formTodo.task;
                    this.todos[i].priority = this.formTodo.priority;
                    this.todos[i].note = this.formTodo.note;
                    this.resetForm();
                    this.dialog = false;
                    return;
                }
            }
            this.todos.push(this.formTodo);
            this.resetForm();
            this.dialog = false;
        },
        cancel() {
            this.resetForm();
            this.dialog = false;
        },
        cancelDelete() {
            this.dialogDelete = false;
        },
        resetForm() {
            this.formTodo = {
                task: null,
                priority: null,
                note: null,
            };
        },
        editItem(item) {
            this.formTodo = item;
            this.dialog = true;
        },
        deleteItem(item){
            this.formTodo = item;
            this.todosSelesai.push(this.formTodo);
            this.todos.splice(this.todos.indexOf(item), 1);
            this.dialogDelete = false;
            this.resetForm();
        },
        close(){
            this.dialogSelesai = false;
        },
        checkItem(item){ //do when user checked or unchecked v-checkbox
            if(item.check == false){
                item.check = true;
            } else {
                item.check = false;
            }
        },
        deleteSelected(){ //check each todos.check, if true do splice
            let i;
            for(i=this.todos.length-1;i>=0;i--){
                if(this.todos[i].check==true){
                    this.formTodo = this.todos[i]; //copy value of item that want to delete
                    this.todosSelesai.push(this.formTodo); //the deleted item pushed into array todosSelesai
                    this.todos.splice(i,1);
                }
            }
            this.resetForm();
        },
        isWantDel(){ //show v-card selected delete if found at least 1 item.check == true
            let i;
            for(i=0;i<this.todos.length;i++) {
                if(this.todos[i].check == true) {
                    return true;
                }
            }
            return false;
        },
    },
};
</script>