<template>
    <v-toolbar color="indigo">
        <v-toolbar-title class="text-uppercase">
            <v-icon start>mdi-vuetify</v-icon>
            Frameworks
        </v-toolbar-title>
        <v-dialog max-width="500" v-model="dialog">
            <template v-slot:activator="{ props }">
                <v-btn variant="outlined" v-bind="props" prepend-icon="mdi-plus-circle">Add</v-btn>
            </template>
            <v-card>
                <v-toolbar color="indigo">
                    <v-toolbar-title>
                        <v-icon start>mdi-plus</v-icon>
                        Add Task
                    </v-toolbar-title>
                </v-toolbar>
                <v-card-text>
                    <v-text-field 
                        v-model="title" 
                        label="Title" 
                        variant="outlined"
                        required
                    ></v-text-field>
                    <v-textarea 
                        v-model="description"
                        label="Description"
                        variant="outlined"
                        :counter="256"
                        no-resize
                        required
                    ></v-textarea>
                    <Datepicker v-model="date"></Datepicker>
                    <v-container fluid>
                        <v-radio-group inline v-model="priority">
                            <template v-slot:label>
                                Priority:
                            </template>
                            <v-radio label="Low" value="low"></v-radio>
                            <v-radio label="Medium" value="medium"></v-radio>
                            <v-radio label="High" value="high"></v-radio>
                        </v-radio-group>
                    </v-container>
                </v-card-text>
                <v-card-actions class="justify-end">
                    <v-btn variant="flat" color="green" prepend-icon="mdi-check" @click="addTask(title, description, date, priority)">Add</v-btn>
                    <v-btn variant="flat" color="red" prepend-icon="mdi-cancel" @click="dialog = false; resetValues()">Cancel</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </v-toolbar>
    <v-table fixed-header>
        <thead>
            <tr>
                <th class="text-center" style="width:20%">Title</th>
                <th class="text-center" style="width:35%">Description</th>
                <th class="text-center" style="width:10%">Deadline</th>
                <th class="text-center" style="width:10%">Priority</th>
                <th class="text-center" style="width:10%">Complete?</th>
                <th class="text-center" style="width:15%">Actions</th>
            </tr>
        </thead>
        <tbody>
            <tr class="text-center" v-for="item in tasks" :key="item.id">
                <td>{{ item.title }}</td>
                <td>{{ item.description }}</td>
                <td>{{ formatDate(item.deadline) }}</td>
                <td>{{ item.priority }}</td>
                <td>
                    <v-checkbox class="d-flex justify-center"></v-checkbox>
                </td>
                <td>
                    <v-tooltip text="Update" location="bottom">
                        <template v-slot:activator="{ props }">
                            <v-btn v-bind="props" variant="text" color="blue" icon="mdi-file-edit"></v-btn>
                        </template>
                    </v-tooltip>
                    <v-tooltip text="Delete" location="bottom">
                        <template v-slot:activator="{ props }">
                            <v-btn v-bind="props" variant="text" color="red" icon="mdi-delete"></v-btn>
                        </template>
                    </v-tooltip>
                    
                </td>
            </tr>
        </tbody>
    </v-table>
    <v-snackbar v-model="addSnackbar" color="success">
        Task added!
        <template v-slot:actions>
            <v-btn variant="text" @click="addSnackbar = false">Close</v-btn>
        </template>    
    </v-snackbar>
</template>

<script>
import Datepicker from '@vuepic/vue-datepicker'
import { uuid } from 'vue-uuid';

export default {
    components: { Datepicker },
    data () {
        return {
            title: null,
            description: null,
            date: null,
            priority: null,
            valid: false,

            dialog: false,
            addSnackbar: false,
            tasks: [
                {
                    title: 'Test',
                    description: 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Veritatis excepturi, est quasi minus possimus, amet iure debitis neque ea, sequi totam atque suscipit assumenda nulla quibusdam porro impedit nemo similique!',
                    deadline: new Date(),
                    priority: 'low',
                    uuid: uuid.v4(),
                }
            ]
        }
    },
    methods: {
        // Validate data, add task to list
        addTask(t, desc, date, p) {
            if(date == null) {
                this.addSnackbar = true;
                return;
            }

            this.tasks.push({ title: t, description: desc, deadline: date, priority: p, uuid: uuid.v4() });
            this.dialog = false;
            this.addSnackbar = true;
            this.resetValues();
        },
        // Clear input fields
        resetValues() {
            this.title = null;
            this.description = null;
            this.date = null;
            this.priority = null;
        },
        // Returns date in MM/DD/YYYY format
        formatDate(d) {
            return (d.getMonth()+1) + '/' + d.getDate() + '/' + d.getFullYear();
        },
    }
}
</script>