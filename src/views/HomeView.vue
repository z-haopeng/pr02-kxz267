<template>
  <MyToolbar />
</template>

<script>
import { defineComponent } from 'vue';

// Components
import MyToolbar from '@/components/MyToolbar.vue';

export default defineComponent({
  name: 'HomeView',

  components: {
    MyToolbar,
  },
});
</script>
