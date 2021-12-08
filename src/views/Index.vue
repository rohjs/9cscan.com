<template>
  <v-container fluid class="px-0 pt-0">
    <div class="search-section">
      <div class="page-wide-wrap px-4">
        <v-row>
          <v-col md="8" class="text-left">
            <div class="search-form">
              <h2 class="search-title">The Nine Chronicles Blockchain Explorer</h2>
              <div>
                <v-text-field
                    class="mt-4"
                    outlined
                    placeholder="Search by Block / Account Address / Tx Hash / Action Symbol"
                    v-model="searchKey"
                    @keydown.enter="searchKeyword(searchKey)"
                >
                  <template v-slot:append>
                    <v-btn depressed color="pointblue" class="rect darken-1" @click="searchKeyword(searchKey)"><v-icon color="white">mdi-magnify</v-icon></v-btn>
                  </template>
                </v-text-field>
              </div>
            </div>
          </v-col>
        </v-row>
      </div>
    </div>
    <div class="page-wide-wrap">
      <div class="text-left px-4">
        <span style="font-weight:500;font-size: 13px;color:#777;">
          <span class="mr-1 text-no-wrap">Price: ${{WncgPrice.toFixed(2)}}<span style="font-size: 10px;">/WNCG</span> <span :class="WncgChange24h < 0 ? 'red--text' : 'primary--text'">({{WncgChange24h.toFixed(2)}}%)</span></span>
          <span class="mx-1" style="color:#aaa;font-size:12px;"> / </span>
          <span class="ml-1 text-no-wrap">Market Cap: ${{Number(WncgMarketCap.toFixed()).toLocaleString()}}</span>
        </span>
      </div>
      <v-row>
        <v-col order="1" order-lg="1" md="4" lg="3" sm="6">
          <v-card class="state-card px-4 pt-2 pb-4" outlined>
            <v-card-title>
              <h5>Block Height</h5>
            </v-card-title>
            <v-card-text style="font-size: 38px;color:var(--v-pointblue-base);font-weight: 600;" class="text-left mt-6">
              <v-skeleton-loader type="heading" v-if="loading"></v-skeleton-loader>
              <span v-else class="text-no-wrap">#{{latestBlockIndex}}</span>
            </v-card-text>
          </v-card>
        </v-col>
        <v-col order="3" order-lg="2" cols="12" lg="6">
          <v-card class="state-card px-4 pt-2 pb-4" outlined>
            <v-card-title class="">
              <h5>
                Network Performance
              </h5>
            </v-card-title>
            <v-row class="px-4">
              <v-col cols="12" sm="4" class="px-1">
                <div class="performance-card">
                  <div v-if="loading" style="margin-bottom: 7px;">
                    <v-progress-circular indeterminate color="#ccc"></v-progress-circular>
                  </div>
                  <div class="value" v-else><label id="avg-block-time"></label>s</div>
                  <span>Avg Block Time</span>
                </div>
              </v-col>
              <v-col cols="12" sm="4" class="px-1">
                <div class="performance-card">
                  <div v-if="loading" style="margin-bottom: 7px;">
                    <v-progress-circular indeterminate color="#ccc"></v-progress-circular>
                  </div>
                  <div class="value" v-else><label id="avg-difficulty"></label></div>
                  <span>Avg difficulty</span>
                </div>
              </v-col>
              <v-col cols="12" sm="4" class="px-1">
                <div class="performance-card">
                  <div v-if="loading" style="margin-bottom: 7px;">
                    <v-progress-circular indeterminate color="#ccc"></v-progress-circular>
                  </div>
                  <div class="value" v-else><label id="avg-tx"></label></div>
                  <span>Avg Tx Per Block</span>
                </div>
              </v-col>
            </v-row>
            <v-card-text class="text-right pt-2">
              <div style="font-size: 12px;color:#aaa;">Average over the last 100 blocks.</div>
            </v-card-text>
          </v-card>
        </v-col>
        <v-col order="2" order-lg="3" md="8" lg="3" sm="6">
          <v-card class="state-card px-4 pt-2 pb-4" outlined min-width="230px">
            <v-card-title>
              <h5>Transaction History</h5>
              <tx-history-chart></tx-history-chart>
            </v-card-title>
          </v-card>
        </v-col>
      </v-row>
      <v-row>
        <v-col md="6">
          <v-card class="px-4 py-2" outlined>
            <v-card-title>
              <h4>Latest Blocks</h4>
            </v-card-title>
            <block-table :blocks="latestBlocks10" :loading="loading"></block-table>
            <v-card-actions class="d-flex justify-center">
              <v-btn color="pointlink" class="rect flex-fill" outlined depressed to="blocks">View all blocks</v-btn>
            </v-card-actions>
          </v-card>
        </v-col>
        <v-col md="6">
          <v-card class="px-4 py-2" outlined>
            <v-card-title>
              <h4>Latest Transactions</h4>
            </v-card-title>
            <transaction-table :transactions="latestTransactions10" :loading="loading"></transaction-table>
            <v-card-actions class="d-flex justify-center">
              <v-btn color="pointlink" class="rect flex-fill" outlined depressed to="transactions">View all transactions</v-btn>
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>
    </div>
  </v-container>
</template>

<script>
import api from "../api"
import {mapGetters} from "vuex"
import BlockTable from "@/components/BlockTable";
import TransactionTable from "@/components/TransactionTable";
import { CountUp } from 'countup.js'
import TxHistoryChart from "@/components/ui/TxHistoryChart";

export default {
    name: 'Index',
    components: {TxHistoryChart, TransactionTable, BlockTable},
    mixins: [],
    data() {
        return {
            searchKey: ''
        }
    },
    computed: {
        ...mapGetters('Block', ['totalTxs', 'avgTx', 'avgBlockTime', 'avgDifficulty', 'latestBlockIndex', 'latestBlocks10', 'latestTransactions10', 'loading']),
        ...mapGetters('Price', ['WncgPrice', 'WncgMarketCap', 'WncgChange24h']),
    },
    beforeDestroy() {
        this.$store.dispatch('Block/syncTx', false)
    },
    async created() {
    },
    methods: {
        $onLoaded() {
            this.init()
        },
        init() {
            this.$store.dispatch('Block/syncTx', true)
            this.$watch('latestBlockIndex', () => {
                this.updateCount()
            })
            this.countAvgBlockTime = new CountUp('avg-block-time', 0, {decimalPlaces: 2})
            this.countAvgDifficulty = new CountUp('avg-difficulty', 0)
            this.countAvgTx = new CountUp('avg-tx', 0, {decimalPlaces: 2})
            this.updateCount()
        },
        updateCount() {
            this.countAvgBlockTime.update(this.avgBlockTime || 0)
            this.countAvgDifficulty.update(this.avgDifficulty || 0)
            this.countAvgTx.update(this.avgTx || 0)
        }
    }
}
</script>

<style scoped lang="scss">
.search-section {
  height: 240px;
  position: relative;
  z-index: 0;
  background-color: var(--v-pointblue-darken2);
  margin-bottom: 0px;
  &:after {
    position: absolute;
    opacity: 0.16;
    z-index:-2;
    left:0px;
    top: 0px;
    background: url("https://nine-chronicles.com/assets/img/hero/1.jpg") center no-repeat;
    background-size: cover;
    content: "";
    width: 100%;
    height: 100%;
  }

  .search-form {
    h2 {
      margin-top: 20px;
      font-size: 26px;
      color: white;
      font-weight: 600;
    }

    ::v-deep .v-text-field {
      .v-input__prepend-inner {
        margin-top: 12px !important;
        margin-left: 12px;
        .v-icon {
          color: var(--v-text-base) !important;
        }
      }

      fieldset {
        display: none;
      }
      .v-input__append-inner {
        margin: 0px !important;
        display: flex;
        height: 56px;
        justify-items: center;
        align-items: center;
        margin-right: -14px !important;
        overflow: hidden;
        border-bottom-right-radius: 4px;
        border-top-right-radius: 4px;
        .v-btn {
          min-height: 56px;
        }
      }
      .v-input__slot {
        background-color: white !important;
        &::before {
          border: 0px !important;
        }
        &::after {
        }
        input {
          color: var(--v-text-base) !important;
          max-height: 44px;
          height: 44px;
          font-size: 16px;
        }
        input::placeholder {
          color: var(--v-text-base) !important;
          font-size: 16px;
        }
      }
    }

  }
}

.state-card {
  height: 200px;
  @media screen and (max-width:599px) {
    height: auto;
    min-height: 200px;
  }
}

.performance-card {
  background-color: #fafafa;
  border: 1px solid #f0f0f0;
  padding: 12px 0px;
  .value {
    font-size: 26px;
    font-weight: 600;
    color: #333;
  }
  span {
    font-size: 12px;
    color: var(--v-text-base);
    font-weight: 500;
  }
}
</style>
