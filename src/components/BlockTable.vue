<template>
  <div class="table-wrapper">
    <v-progress-linear
      indeterminate
      height="2"
      color="pointblue"
      v-if="loading"
    >
    </v-progress-linear>

    <ol class="table-list">
      <template>
        <li class="table-item block-item loading" v-if="loading">
          <v-progress-circular color="pointblue" width="2" indeterminate>
          </v-progress-circular>
        </li>

        <li class="table-item block-item empty" v-else-if="blocks.length == 0">
          <span class="grey--text">No Blocks</span>
        </li>

        <transition-group name="list-complete" v-else>
          <li
            v-for="block in blocks"
            :key="block.index"
            class="table-item block-item"
          >
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
              >
                {{ block.transactionCount }}
              </router-link>
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
@import './table.scss';
</style>
