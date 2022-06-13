<template>
  <v-app>
    <v-main>
      <v-container
        fluid
        fill-height
      >
        <v-row
          justify="end"
        >
          <v-btn
            class="mx-3"
            @click="connect"
          >
            <span
              v-if="account"
              v-text="`${account.substring(0, 6)}...${account.substring(account.length - 4)}`"
            />
            <span
              v-else
              v-text="'Connect'"
            />
          </v-btn>
        </v-row>
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
                    :sm="6"
                    :lg="6"
                  >
                    <v-card
                      :height="500"
                      outlined
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
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import { ethers } from 'ethers';
import AppAlert from '@/components/AppAlert.vue';

export default {
  name: 'App',
  components: {
    AppAlert,
  },
  data: () => ({
    message: null,
    account: '',
  }),
  computed: {
    //
  },
  watch: {
    //
  },
  created() {
    if (!window.ethereum) {
      console.log('Please connect to Metamask.');
      return;
    }
    this.init();
  },
  methods: {
    async init() {
      await this.loadWeb3Provider();
      await this.loadAccount();
    },
    loadWeb3Provider() {
      this.web3Provider = new ethers.providers.Web3Provider(window.ethereum);
      this.web3Provider.provider.on('accountChanged', () => this.init());
    },
    async loadAccount() {
      const [account] = await this.web3Provider.send('eth_requestAccounts');
      this.account = account;
    },
    connect() {
      this.loadAccount();
    },
  },
};
</script>
