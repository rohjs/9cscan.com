<template>
  <div class="block-table">
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
          <v-progress-circular color="pointblue" width="2" indeterminate>
          </v-progress-circular>
        </li>

        <li v-else-if="blocks.length == 0">
          <div>
            <span class="grey--text">No Blocks</span>
          </div>
        </li>

        <transition-group name="list-complete" v-else>
          <li v-for="block in blocks" :key="block.index" class="row-item">
            <div>
              <v-btn
                small
                class="rect pointlink-btn"
                outlined
                depressed
                :to="{ name: 'block', params: { index: block.index } }"
                >{{ block.index }}
              </v-btn>
            </div>

            <div v-if="detail">{{ shortAddress(block.hash) }}</div>

            <div class="text-no-wrap">
              {{ moment(block.timestamp).fromNow() }}
            </div>

            <div>
              <router-link
                :to="{
                  name: 'block',
                  params: { index: block.index },
                  hash: '#tx',
                }"
                >{{ block.transactionCount }}</router-link
              >
            </div>

            <div>
              <router-link
                :to="{
                  name: 'account',
                  query: { t: 'mined' },
                  params: { address: block.miner },
                }"
              >
                {{ block.miner.substr(0, 8) }}
              </router-link>
            </div>

            <div v-if="detail">{{ shortAddress(block.nonce) }}</div>
            <div v-if="detail">{{ shortAddress(block.stateRootHash) }}</div>
            <div>{{ block.difficulty }}</div>
          </li>
        </transition-group>
      </template>
    </ol>
  </div>
</template>

<script>
export default {
  name: 'BlockTable',
  components: {},
  props: {
    loading: {
      type: Boolean,
      default: false,
    },
    blocks: {
      type: Array,
      default: [],
    },
    detail: {
      type: Boolean,
      default: false,
    },
  },
  mixins: [],
  data() {
    return {}
  },
  computed: {},
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
  opacity: 0.6;
  background-color: #f5f5fa;
}

.list-complete-leave-active {
  transition: none;
  position: absolute;
  opacity: 0;
}
</style>
