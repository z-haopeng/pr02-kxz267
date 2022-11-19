<template>
  <TaskTable />
</template>

<script>
import { defineComponent } from 'vue';

// Components
import TaskTable from '@/components/TaskTable.vue'

export default defineComponent({
  name: 'HomeView',

  components: {
    TaskTable
  },
});
</script>
