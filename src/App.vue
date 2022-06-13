<template>
  <v-app>
    <v-main>
      <v-container fluid>
        <v-row justify="end">
          <v-btn class="ma-3" @click="connect">
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
        <v-row v-if="remainingSeconds > 0" justify="center">
          <v-col :cols="8">
            <v-row justify="center">
              <v-col :cols="12" class="text-center">
                Countdown to Lauch!
              </v-col>
              <v-col :cols="3" class="text-center">
                {{ remainingTime.days }} Days
              </v-col>
              <v-col :cols="3" class="text-center">
                {{ remainingTime.hours }} Hours
              </v-col>
              <v-col :cols="3" class="text-center">
                {{ remainingTime.minutes }} Minutes
              </v-col>
              <v-col :cols="3" class="text-center">
                {{ remainingTime.seconds }} Seconds
              </v-col>
            </v-row>
          </v-col>
        </v-row>
        <v-row justify="center">
          <v-col :cols="10">
            <div class="text-center text-h5 font-weight-re my-6 cursor-default">
              Mushroom Farm
            </div>
          </v-col>
          <v-col :cols="10">
            <v-row>
              <v-col :cols="12" :sm="6" :lg="6">
                <v-card :height="500" outlined>
                  <v-card-title class="text-uppercase">Market</v-card-title>
                  <v-card-text>
                    <v-row>
                      <v-col :cols="6" class="text-uppercase">Contract</v-col>
                      <v-col :cols="6" class="text-uppercase text-end">{{ contractBalance }} BNB</v-col>
                    </v-row>
                    <v-row>
                      <v-col :cols="6" class="text-uppercase">Wallet</v-col>
                      <v-col :cols="6" class="text-uppercase text-end">{{ walletBalance }} BNB</v-col>
                    </v-row>
                    <v-row>
                      <v-col :cols="6" class="text-uppercase">Your Mushrooms</v-col>
                      <v-col :cols="6" class="text-uppercase text-end">{{ mushroomCount }}</v-col>
                    </v-row>
                    <v-row>
                      <v-col :cols="12">
                        <v-text-field
                          v-model="amount"
                          :min="0"
                          autofocus
                          dense
                          hide-details
                          outlined
                          solo
                          suffix="BNB"
                          type="number"
                          class="mb-3"
                        />
                        <v-btn block :disabled="!account" @click="buyMushrooms">
                          Buy
                        </v-btn>
                      </v-col>
                    </v-row>
                    <v-row>
                      <v-col :cols="6" class="text-uppercase">Your Rewards</v-col>
                      <v-col :cols="6" class="text-uppercase text-end">0 BNB</v-col>
                    </v-row>
                    <v-row>
                      <v-col :cols="6">
                        <v-btn block :disabled="!account">
                          Multiply
                        </v-btn>
                      </v-col>
                      <v-col :cols="6">
                        <v-btn block :disabled="!account">
                          Sell
                        </v-btn>
                      </v-col>
                    </v-row>
                  </v-card-text>
                </v-card>
              </v-col>
              <v-col :cols="12" :sm="6" :lg="6">
                <v-card :height="220" outlined class="mb-6">
                  <v-card-title class="text-uppercase">Nutrition</v-card-title>
                  <v-card-text>
                    <v-row>
                      <v-col :cols="6" class="text-uppercase">Daily Return</v-col>
                      <v-col :cols="6" class="text-uppercase text-end">10%</v-col>
                    </v-row>
                    <v-row>
                      <v-col :cols="6" class="text-uppercase">APR</v-col>
                      <v-col :cols="6" class="text-uppercase text-end">3650%</v-col>
                    </v-row>
                    <v-row>
                      <v-col :cols="6" class="text-uppercase">Dev Fee</v-col>
                      <v-col :cols="6" class="text-uppercase text-end">2%</v-col>
                    </v-row>
                  </v-card-text>
                </v-card>
                <v-card :height="280 - 24" outlined>
                  <v-card-title class="text-uppercase">Referral Link</v-card-title>
                  <v-card-text>
                    Earn 15% of the BNB used to multiply mushroom from anyone who uses your referral link.
                  </v-card-text>
                </v-card>
              </v-col>
            </v-row>
          </v-col>
          <v-col :cols="12">
            <div class="caption text-center grey--text text--disabled my-6">
              <span v-text="`White Paper`" class="cursor-pointer"/>
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
import moment from 'moment';
import { ethers } from 'ethers';
import AppAlert from '@/components/AppAlert.vue';
import MushroomFarm from '@/contracts/MushroomFarm.json';

export default {
  name: 'App',
  components: {
    AppAlert,
  },
  data: () => ({
    message: null,
    /**
     * contract data
     */
    web3Provider: null,
    account: '',
    startTime: '',
    contractBalance: '',
    walletBalance: '',
    mushroomCount: '',
    remainingTime: null,
    remainingSeconds: '',
    /**
     * form data
     */
    amount: '',
  }),
  computed: {
    decimals() {
      return 18;
    },
    signer() {
      return this.web3Provider.getSigner();
    },
    contract() {
      return new ethers.Contract(process.env.VUE_APP_CONTRACT_ADDRESS, MushroomFarm.abi, this.signer);
    },
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
      if (this.account) await this.loadData();
    },
    loadWeb3Provider() {
      this.web3Provider = new ethers.providers.Web3Provider(window.ethereum);
      this.web3Provider.provider.on('accountsChanged', () => this.init());
    },
    async loadAccount() {
      const [account] = await this.web3Provider.send('eth_requestAccounts');
      this.account = account;
    },
    async loadData() {
      const startTime = await this.contract.startTime();
      let duration = moment.duration((startTime - (+new Date()) / 1000) * 1000, 'milliseconds');
      setInterval(() => {
        duration = moment.duration(duration - 1000, 'milliseconds');
        this.remainingTime = {
          days: Math.floor(duration.asDays()),
          hours: duration.hours(),
          minutes: duration.minutes(),
          seconds: duration.seconds(),
        };
        this.remainingSeconds = Math.floor(duration.asSeconds());
      }, 1000);
      this.contractBalance = Number(await this.contract.getBalance() / ethers.BigNumber.from(10).pow(this.decimals)).toFixed(3);
      this.walletBalance = Number(await this.web3Provider.getBalance(this.account) / ethers.BigNumber.from(10).pow(this.decimals)).toFixed(3);
      this.mushroomCount = Number(await this.contract.getMyMushrooms()).toLocaleString();
      const claimedMushrooms = await this.contract.claimedMushrooms(this.account);
      const myMushrooms = await this.getMushroomsSinceLastHarvest();
      this.mushroomCount = claimedMushrooms + myMushrooms;
    },
    connect() {
      this.loadAccount();
    },
    async getMushroomsSinceLastHarvest() {
      const period = await this.contract.period();
      const lastHarvest = await this.contract.lastHarvest(this.account);
      const farmers = await this.contract.farmers(this.account);
      return Math.min(period, Math.floor(+new Date() / 1000) - lastHarvest) * farmers;
    },
    async buyMushrooms() {
      const amount = ethers.BigNumber.from(1).mul(ethers.FixedNumber.fromString(this.amount));
      const res = await this.contract.buyMushrooms(this.account, {
        value: amount,
      });
      await res.wait();
      window.location.reload();
    },
  },
};
</script>

<style lang="scss">
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
}
</style>
