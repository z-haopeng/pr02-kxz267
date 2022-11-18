<template>
    <v-toolbar color="indigo">
        <v-toolbar-title class="text-uppercase">
            <v-icon start>mdi-vuetify</v-icon>
            Frameworks
        </v-toolbar-title>
        <v-dialog max-width="500" v-model="dialog">
            <template v-slot:activator="{ props }">
                <v-btn variant="outlined" v-bind="props">
                    <v-icon start>mdi-plus-circle</v-icon>
                    Add
                </v-btn>
            </template>
            <v-card>
                <v-toolbar color="indigo">
                    <v-toolbar-title>
                        <v-icon start>mdi-pencil</v-icon>
                        Add Task
                    </v-toolbar-title>
                </v-toolbar>
                <v-card-text>
                    <div class="text-h2 pa-12">Hello world!</div>
                </v-card-text>
                <v-card-actions class="justify-end">
                    <v-btn text @click="dialog = false">Close</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </v-toolbar>
</template>

<script>
export default {
    data() {
        return {
            dialog: false,
        }
    }
}
</script>