<template>
  <v-snackbar
    :color="color"
    :value="true"
    text
    @input="$emit('onClose')"
  >
    <span
      v-text="text"
    />
  </v-snackbar>
</template>

<script>
export default {
  props: {
    color: {
      type: String,
      required: true,
    },
    text: {
      type: String,
      required: true,
    },
  },
};
</script>
