<template>
  <v-row
    justify="center"
    no-gutters
  >
    <v-col
      :cols="10"
    >
      <div
        class="text-center text-h5 font-weight-re my-6 cursor-default"
      >
        Mushroom Farm
      </div>
    </v-col>
    <v-col
      :cols="10"
    >
      <v-card>
        <v-card-text
          class="pa-6"
        >
          <v-row>
            <v-col
              :cols="12"
              :sm="6"
              :lg="6"
            >
              <v-card
                :height="500"
                outlined
                class="mb-6"
              >
                <v-card-text
                  class="pa-0"
                >
                  Card A
                </v-card-text>
              </v-card>
            </v-col>
            <v-col
              :cols="12"
              :sm="12"
              :lg="6"
            >
              <v-card
                :height="500"
                outlined
                class="mb-6"
              >
                <v-card-text
                  class="pa-0"
                >
                  Card A
                </v-card-text>
              </v-card>
            </v-col>
          </v-row>
        </v-card-text>
      </v-card>
    </v-col>
    <v-col
      :cols="12"
    >
      <div
        class="caption text-center grey--text text--disabled my-6"
      >
        <span
          v-text="`White Paper`"
          class="cursor-pointer"
        />
      </div>
    </v-col>
    <AppAlert
      v-if="message"
      :color="message.success ? 'success' : 'error'"
      :text="message.text"
      @onClose="setMessage(null)"
    />
  </v-row>
</template>

<script>
// import axios from '@/plugins/axios';
// import moment from 'moment';
import AppAlert from '@/components/AppAlert.vue';

export default {
  name: 'AppForm',
  components: {
    AppAlert,
  },
  data: () => ({
    //
  }),
  computed: {
    //
  },
  watch: {
    //
  },
  created() {
    //
  },
  methods: {
    //
  },
};
</script>

<style lang="scss" scoped>
</style>
