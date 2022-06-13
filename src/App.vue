<template>
  <v-app>
    <v-main>
      <v-container
        fluid
        fill-height
        class="py-0"
      >
        <AppForm/>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import AppForm from './components/AppForm.vue';

export default {
  name: 'App',
  components: {
    AppForm,
  },
};
</script>
