<template>
  <oz-table
    :rows="currentRows"
    :total-pages="100"
    :current-page="currentPage"
    :sortRows="sortRows"
    :static-paging="true"
    @getPage="getPage"
  >
    <!-- статичная пагинация
    :static-paging="true"
    @getPage="getPage"

    бесконечная пагинация
    :static-paging="false" 
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
      isFetchData: false
    };
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
      return;
    },
    getPage(number, offset = 5) {
      this.currentRows = this.sortedRows.slice((number * offset) - offset, (number * offset));
      this.currentPage = number;
    },
    async infGetPage() {
      this.blockingPromise && await this.blockingPromise;
      if(!this.isFetchData) {
        const res = await fetch(`https://jsonplaceholder.typicode.com/comments`);
        this.rows = await res.json();
        this.sortedRows = this.rows;
        this.isFetchData = true;
      }
      const newRows = this.sortedRows.slice(((this.currentPage + 1) * 5) - 5, ((this.currentPage + 1) * 5));
      this.currentRows = [...this.currentRows, ...newRows];
      this.currentPage++;
    }
  }
};
</script>
