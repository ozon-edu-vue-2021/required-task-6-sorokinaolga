<template>
  <oz-table
    :rows="currentRows"
    :total-pages="numberOfPages"
    :current-page="currentPage"
    :sortRows="sortRows"
    :static-paging="isStaticPaging" 
    @getPage="getPage"
  >
    <!-- статичная пагинация
    isStaticPaging="true"
    @getPage="getPage"

    бесконечная пагинация
    isStaticPaging="false" 
    @getPage="infGetPage" -->
  
    <oz-table-column prop="id" title="ID" />
    <oz-table-column prop="postId" title="Post ID" />

    <oz-table-column prop="email">
      <template #title>
        <b>Email</b>
      </template>

      <template #body="{ row }">
        <a :href="`mailto:${row.email}`">{{ row.email }}</a>
      </template>
    </oz-table-column>

    <oz-table-column prop="name" title="Name" />
  </oz-table>
</template>

<script>
import { orderBy } from 'lodash/collection';
import OzTable from './oz-table';
import OzTableColumn from './oz-table-column';

export default {
  name: 'TableComponent',
  components: {
    OzTableColumn,
    OzTable
  },
  async created() {
    const res = await fetch(`https://jsonplaceholder.typicode.com/comments`);
    this.rows = await res.json();
    this.sortedRows = this.rows;
    this.getPage(1);
  },
  data() {
    return {
      rows: [],
      sortedRows: [],
      currentRows: [],
      currentPage: 1,
      offset: 5,
      isStaticPaging: true,
      isFetchData: false,
    };
  },
  computed: {
    numberOfPages() {
      return Math.ceil(this.sortedRows.length / this.offset);
    }
  },
  methods: {
    sortRows(sortProp, sortDirection, filterText, filterProp) {
      let res;
      if (!sortProp) {
        res =  this.rows;
      }
      res = orderBy(this.rows, [sortProp], [sortDirection]);
      if(filterText) {
        res = res.filter(row => row[filterProp].toString().toLowerCase().search(filterText) > -1)
      }
      this.sortedRows = res;
      this.getPage(1);
    },
    getPage(number) {
      if(!this.isStaticPaging) this.offset = 10;
      this.currentRows = this.sortedRows.slice((number * this.offset) - this.offset, (number * this.offset));
      this.currentPage = number;
    },
    async infGetPage() {
      this.offset = 10;
      this.blockingPromise && await this.blockingPromise;
      if(!this.isFetchData) {
        const res = await fetch(`https://jsonplaceholder.typicode.com/comments`);
        this.rows = await res.json();
        this.sortedRows = this.rows;
        this.isFetchData = true;
      }
      const newRows = this.sortedRows.slice(((this.currentPage + 1) * this.offset) - this.offset, ((this.currentPage + 1) * this.offset));
      this.currentRows = [...this.currentRows, ...newRows];
      this.currentPage++;
    }
  }
};
</script>
