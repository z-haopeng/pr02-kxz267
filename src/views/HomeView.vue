<template>
  <MyToolbar />
  <TaskTable />
</template>

<script>
import { defineComponent } from 'vue';

// Components
import MyToolbar from '@/components/MyToolbar.vue';
import TaskTable from '@/components/TaskTable.vue'

export default defineComponent({
  name: 'HomeView',

  components: {
    MyToolbar,
    TaskTable
  },
});
</script>
