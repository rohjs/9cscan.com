<template>
  <div class="table-wrap">
    <v-progress-linear
      indeterminate
      height="2"
      color="pointblue"
      v-if="loading"
    >
    </v-progress-linear>

    <ol>
      <template>
        <li v-if="loading">
          <v-progress-circular
            color="pointblue"
            width="2"
            indeterminate
          ></v-progress-circular>
        </li>

        <li v-else-if="transactions.length == 0">
          <div>
            <span class="grey--text">No Transactions</span>
          </div>
        </li>

        <transition-group name="list-complete" v-else>
          <li v-for="tx in transactions" :key="tx.id" class="row-item">
            <div>
              <router-link :to="{ name: 'transaction', params: { id: tx.id } }">
                {{ shortAddress(tx.id) }}
              </router-link>
            </div>

            <div>
              <v-btn
                class="rect pointlink-btn"
                outlined
                small
                depressed
                :to="{ name: 'block', params: { index: tx.blockIndex } }"
                >{{ tx.blockIndex }}</v-btn
              >
            </div>

            <div class="text-no-wrap">{{ moment(tx.timestamp).fromNow() }}</div>

            <div v-if="detail">
              <v-chip label small outlined text-color="#555">
                <strong class="mr-1">{{
                  latestBlockIndex - tx.blockIndex + 1
                }}</strong>
                Confirms
              </v-chip>
            </div>

            <div>
              <router-link
                :to="{ name: 'account', params: { address: tx.signer } }"
              >
                {{ tx.signer.substr(0, 8) }}
              </router-link>
            </div>

            <div v-if="involved">
              <v-chip
                label
                small
                color="success"
                :outlined="tx.involved['type'] == 'INVOLVED'"
                v-if="tx.involved"
                >{{ tx.involved['type'] }}</v-chip
              >
            </div>

            <div>
              <v-btn
                label
                small
                :to="{ name: 'transaction', params: { id: tx.id } }"
                style="
                  text-transform: none;
                  height: 20px;
                  font-weight: 500;
                  letter-spacing: 0;
                "
                color="point"
                rounded
                outlined
                v-for="action in tx.actions"
                v-if="action.inspection"
                class="darken-1 px-2 mx-1"
              >
                <v-btn
                  icon
                  x-small
                  color="point"
                  :to="{
                    query: { action: action.inspection['typeId'] },
                    ...(detail ? {} : { name: 'transactions' }),
                  }"
                  style="width: 12px"
                  class="mr-1"
                  ><v-icon x-small>mdi-filter-variant</v-icon></v-btn
                >
                {{ action.inspection['typeId'] }}
              </v-btn>
            </div>

            <div v-if="detail">
              <router-link
                :to="{
                  name: 'avatar',
                  params: { address: action.inspection['avatarAddress'] },
                }"
                v-for="action in tx.actions"
                v-if="action.inspection"
                class="mx-1"
              >
                {{
                  action.inspection['avatarAddress'] &&
                  action.inspection['avatarAddress'].substr(0, 8)
                }}
              </router-link>
            </div>

            <div v-if="detail">{{ tx.updatedAddresses.length }}</div>
          </li>
        </transition-group>
      </template>
    </ol>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'

export default {
  name: 'TransactionTable',
  components: {},
  props: {
    loading: {
      type: Boolean,
      default: false,
    },
    transactions: {
      type: Array,
      default: [],
    },
    detail: {
      type: Boolean,
      default: false,
    },
    involved: {
      type: Boolean,
      default: false,
    },
  },
  mixins: [],
  data() {
    return {}
  },
  computed: {
    ...mapGetters('Block', ['latestBlockIndex']),
  },
  beforeDestroy() {},
  async created() {},
  methods: {},
}
</script>

<style scoped lang="scss">
.table-wrap {
  background-color: white;
}
.row-item {
  transition: all 0.5s;
}
.list-complete-enter,
.list-complete-leave-to {
  transform: translateY(-40px);
  background-color: #f5f5fa;
}
.list-complete-leave-active {
  transition: none;
  position: absolute;
  opacity: 0;
}
</style>
