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
          <b-row id="pagination">
            <b-col>
              <b-row>
                <b-col>
                  <b-form-group id="display-selection" horizontal label="Display:">
                    <b-form-select :options="pageSizeOptions" v-model="perPage" style="width:60px; float:right; margin-right: 10px"/>
                  </b-form-group>
                </b-col>

                <b-col>
                  <b-form-group id="page-selection" horizontal label="Page:">
                    <b-form-select :options="pageNumbers" v-model="currentPage" style="width:60px; float:right"/>
                  </b-form-group>
                </b-col>

                <b-col>
                  <div id="prev-next-page">
                    <span>of {{this.numberOfPages}}</span>
                    <b-button-group>
                      <b-btn id="previous" @click="previousPage">&lsaquo;</b-btn>
                      <b-btn id="next" @click="nextPage">&rsaquo;</b-btn>
                    </b-button-group>
                  </div>
                </b-col>
              </b-row>
            </b-col>
            <b-col></b-col>
          </b-row>
          <div id="results" v-if="!isEmpty(searchList)" v-for="result in searchList">
            <h4 class="word">{{ result.word }}</h4>
            <p v-if="isEmpty(result.conflicts)" class="conflicts">
              ***
            </p>
            <p v-else  v-for="conflict in result.conflicts" class="conflicts">
              {{ conflict }}
            </p>
          </div>
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
      'conflicts': ['blanlan','ah3u4hf','testing bla', 'test conflict']
    },
    {
      'word': 'result2',
      'conflicts': ['blanlan2','ah3u4hf2','testing bla2', 'test conflict2']
    },
    {
      'word': 'result3',
      'conflicts': ['blanlan3','ah3u4hf3','testing bla3', 'test conflict3']
    },
    {
      'word': 'result4',
      'conflicts': []
    },
    {
      'word': 'result11',
      'conflicts': ['blanlan','ah3u4hf','testing bla', 'test conflict']
    },
    {
      'word': 'result22',
      'conflicts': ['blanlan2','ah3u4hf2','testing bla2', 'test conflict2']
    },
    {
      'word': 'result33',
      'conflicts': ['blanlan3','ah3u4hf3','testing bla3', 'test conflict3']
    },
    {
      'word': 'result44',
      'conflicts': []
    },
  ]
}
import axios from '@/axios-auth'
export default {
  name: "Cobrs",
  data: function () {
    return {
      searchStr: '',
      searchList: [],
      perPage: 10,
      pageSizeOptions: [5, 10, 15, 20, 25, 50, 100],
      currentPage: 1,
      numberOfPages: 1,
      pageNumbers: [1],
      total: 0,
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
      console.log('test: ', this.test);
      this.$store.state.cobrsJSON = this.test;
      // this.$store.dispatch('cobrsSearch',this.searchStr);
    },
    isEmpty(val) {
      if (val.length > 0)
        return false;
      return true;
    },
    buildPages() {
      let pages = [1];
      let count = 1;
      for (let i=this.perPage; i<this.total;i+=this.perPage) {
        count++;
        pages.push(count);
      }
      this.numberOfPages = count;
      return pages;
    },
    previousPage() {
      if (this.currentPage === 1)
        $('#previous').prop('disabled', true);
      else {
        this.currentPage--;
      }
    },
    nextPage() {
      if (this.currentPage === this.pageNumbers[this.pageNumbers.length-1])
        $('#next').prop('disabled', true);
      else
        this.currentPage++;
    },
  },
  watch: {
    resultsJSON: function (val) {
      console.log('resultsJSON watcher fired: ', val);
      if (val != null) {
        this.searchList = val.list;
        this.total = val.list.length;
      }
      else {
        this.searchList = [];
        this.currentPage = 1;
        this.numberOfPages = 1;
        this.pageNumbers = [1];
        this.total = 0;
      }
    },
    perPage: function (val) {
      console.log('perPage watcher fired: ', val);
      this.pageNumbers = this.buildPages();
      let offset = (this.currentPage-1) * val;
      this.searchList = this.resultsJSON.list.slice(offset,offset + val);
    },
    currentPage: function (val) {
      console.log('currentPage watcher fired: ', val);
      if (val === 1) {
        $('#previous').prop('disabled', true);
        $('next').prop('disabled', false);
      } else if (val === this.pageNumbers[this.pageNumbers.length-1]) { //if val == last page
        $('#previous').prop('disabled', false);
        $('next').prop('disabled', true);
      } else {
        $('#previous').prop('disabled', false);
        $('next').prop('disabled', false);
      }
      let offset = (val-1) * this.perPage;
      this.searchList = this.resultsJSON.list.slice(offset,offset + this.perPage);
    },
    searchList: function (val) {
      console.log('searchList watcher fired: ', val);
    },
  },
}
</script>

<style scoped>
  .search{
    width: 100%;
    max-width: 800px;
    font-size: 25px;
  }
  #results {
    border-bottom: 1px solid #1b1e21;
    margin-top: 20px;
    padding: 20px;
    text-align: center;
  }
  .word {
    color: #1e7e34;
    font-size: 30px;
  }
  .conflicts {
    color: #ffc107;
    font-size: 20px;
  }
  #display-selection {
    width:118px;
    margin-top: 20px;
    margin-bottom: 0;
    margin-right: 0;
  }
  #page-selection {
    width:95px;
    float: right;
    margin-right:5px;
    margin-top: 20px;
    margin-bottom: 0;
  }
  #prev-next-page {
    float:left;
    margin-top: 20px;
    margin-bottom: 0;
  }
  .btn-secondary {
    background-color: #ffffff;
    border: 1px solid #ced4da;
    border-radius: .2rem;
    color: #7f7f7f;
    height: 37px;
    padding-top: 2px;
  }
  .btn-secondary:hover{
    background-color: #ced4da;
  }
</style>
