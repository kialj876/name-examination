<!--eslint-disable-->
<template>
  <div>
      <div class="container-fluid">
        <h1>COBRS Search</h1>
        <div>
          <form class="form-inline" @submit.prevent="onSubmit">
            <input ref="search" type="text" class="search form-control" v-model="searchStr" tabindex="1">
            <button class="btn-search" type="submit"><i class="fa fa-search" tabindex="8"/></button>
            <button class="btn-reset" @click="resetSearchStr" tabindex="7">
              <i class="fa fa-times" /></button>
          </form>
        </div>
      </div>
    </div>
</template>

<script>
/* eslint-disable */
let testJSON = {
  'list': [
    {
      'word': 'result1',
      'hits': ['blanlan','ah3u4hf','testing bla', 'test conflict']
    },
    {
      'word': 'result2',
      'hits': ['blanlan2','ah3u4hf2','testing bla2', 'test conflict2']
    },
    {
      'word': 'result3',
      'hits': ['blanlan3','ah3u4hf3','testing bla3', 'test conflict3']
    },
  ]
}
import axios from '@/axios-auth'
export default {
  name: "Cobrs",
  data: function () {
    return {
      searchStr: '',
      searchList: null,
      test: null,
    }
  },
  computed: {
    resultsJSON() {
      return this.$store.getters.cobrsJSON;
    }
  },
  mounted: function () {
    this.test = testJSON;
  },
  methods: {
    resetSearchStr() {
      this.searchStr = '';
      this.test = null;
    },
    onSubmit() {
      console.log('submitted searchStr');
      this.$store.state.cobrsJSON = this.test;
      // this.$store.dispatch('cobrsSearch',this.searchStr);
    }
  },
  watch: {
    resultsJSON: function (val) {
      console.log('resultsJSON watcher fired: ', val);
      if (val != null)
        this.searchList = val.list;
    }
  },
}
</script>

<style scoped>
  .search{
    width: 80%;
    max-width: 800px;
    font-size: 25px;
  }
</style>
