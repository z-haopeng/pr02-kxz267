<template>
    <v-toolbar color="indigo">
        <v-toolbar-title class="text-uppercase">
            <v-icon start>mdi-vuetify</v-icon>
            Frameworks
        </v-toolbar-title>
        <v-btn variant="outlined" prepend-icon="mdi-plus-circle"
            @click="updating = false; resetValues(); dialog = true">
            Add</v-btn>
        <v-dialog max-width="500" v-model="dialog">
            <v-card>
                <v-toolbar color="indigo">
                    <v-toolbar-title>
                        <div v-if="updating">
                            <v-icon start>mdi-pencil</v-icon>
                            Edit Task
                        </div>
                        <div v-else>
                            <v-icon>mdi-plus</v-icon>
                            Add Task
                        </div>
                    </v-toolbar-title>
                </v-toolbar>
                <v-card-text>
                    <v-form v-model="valid">
                        <v-text-field v-model="title" :rules="titleRules" :counter="32" :disabled="updating"
                            label="Title" variant="outlined" class="my-2"></v-text-field>
                        <v-textarea v-model="description" :rules="descriptionRules" :counter="256" label="Description"
                            variant="outlined" class="my-2" no-resize></v-textarea>
                        <Datepicker v-model="date"></Datepicker>
                        <v-container fluid>
                            <v-radio-group v-model="priority" :rules="priorityRules" inline>
                                <template v-slot:label>
                                    Priority:
                                </template>
                                <v-radio label="Low" value="low"></v-radio>
                                <v-radio label="Medium" value="medium"></v-radio>
                                <v-radio label="High" value="high"></v-radio>
                            </v-radio-group>
                        </v-container>
                    </v-form>
                </v-card-text>
                <v-card-actions class="justify-end">
                    <v-btn v-if="updating" :disabled="!description || description.length > 256 || !priority || !date"
                        variant="flat" color="success" prepend-icon="mdi-file-edit" @click="updateTask(uuid)">
                        Update
                    </v-btn>
                    <v-btn v-else :disabled="!valid || !date" variant="flat" color="success" prepend-icon="mdi-check"
                        @click="createTask()">
                        Add
                    </v-btn>
                    <v-btn variant="flat" color="error" prepend-icon="mdi-cancel" @click="dialog = false;">
                        Cancel
                    </v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </v-toolbar>

    <v-table>
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
                    <v-checkbox v-model="item.complete" class="d-flex justify-center"></v-checkbox>
                </td>
                <td>
                    <v-tooltip text="Update" location="bottom">
                        <template v-slot:activator="{ props }">
                            <v-btn :disabled="item.complete" class="mx-1" v-bind="props" variant="text" color="blue"
                                icon="mdi-file-edit"
                                @click="updating = true; initializeUpdate(item.uuid); dialog = true">
                            </v-btn>
                        </template>
                    </v-tooltip>
                    <v-tooltip text="Delete" location="bottom">
                        <template v-slot:activator="{ props }">
                            <v-btn class="mx-1" v-bind="props" variant="text" color="red" icon="mdi-delete"
                                @click="deleteTask(item.uuid)"></v-btn>
                        </template>
                    </v-tooltip>
                </td>
            </tr>
        </tbody>
    </v-table>

    <v-snackbar v-model="successSnackbar" color="success" timeout="2000">
        {{ successText }}
        <template v-slot:actions>
            <v-btn variant="text" @click="successSnackbar = false">Close</v-btn>
        </template>
    </v-snackbar>
</template>

<script>
import Datepicker from '@vuepic/vue-datepicker'
import { uuid } from 'vue-uuid';

export default {
    components: { Datepicker },
    data() {
        return {
            title: null,
            description: null,
            date: null,
            priority: null,
            uuid: null,
            valid: false,
            updating: false,
            titleRules: [
                v => !!v || 'Title is required',
                v => v.length <= 32 || 'Title must be under 32 characters',
                v => this.updating || this.tasks.filter((t) => t.title == v).length == 0 || 'Title must be unique'
            ],
            descriptionRules: [
                v => !!v || 'Description is required',
                v => v.length <= 256 || 'Description must be under 256 characters'
            ],
            priorityRules: [
                v => !!v || 'Priority is required',
            ],

            dialog: false,
            successSnackbar: false,
            successText: '',
            tasks: [
                // {
                //     title: 'Test',
                //     description: 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Veritatis excepturi, est quasi minus possimus, amet iure debitis neque ea, sequi totam atque suscipit assumenda nulla quibusdam porro impedit nemo similique!',
                //     deadline: new Date(),
                //     priority: 'low',
                //     complete: false,
                //     uuid: uuid.v4(),
                // }
            ]
        }
    },
    methods: {
        // Validate date, add task to list
        createTask() {
            this.tasks.push({ title: this.title, description: this.description, deadline: this.date, priority: this.priority, complete: false, uuid: uuid.v4() });
            this.dialog = false;
            this.successText = 'Task added!';
            this.successSnackbar = true;
        },
        updateTask(id) {
            var task = this.tasks.find((t) => id == t.uuid);
            task.description = this.description;
            task.deadline = this.date;
            task.priority = this.priority;
            this.dialog = false;
            this.successText = 'Task updated!';
            this.successSnackbar = true;
        },
        deleteTask(id) {
            this.tasks = this.tasks.filter((t) => id != t.uuid);
            this.successText = 'Task deleted!';
            this.successSnackbar = true;
        },
        initializeUpdate(id) {
            var task = this.tasks.find((t) => t.uuid == id);
            this.title = task.title;
            this.description = task.description;
            this.date = task.deadline;
            this.priority = task.priority;
            this.uuid = task.uuid;
        },
        // Clear input fields
        resetValues() {
            this.title = null;
            this.description = null;
            this.date = null;
            this.priority = null;
            this.uuid = null;
        },
        // Returns date in MM/DD/YYYY format
        formatDate(d) {
            return (d.getMonth() + 1) + '/' + d.getDate() + '/' + d.getFullYear();
        },
    }
}
</script>