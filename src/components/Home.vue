<template>
  <v-container>
    <v-data-table
      class="elevation-1"
      :headers="tableHeaders"
      :items="tableData"
      :items-per-page="5"
      :loading="isTableLoading">
      <template v-slot:top>
        <v-btn
          block
          outlined
          @click="addRow"
        >Add Row</v-btn>
      </template>
      <!-- JSON prettify HTTP Response -->
      <template v-slot:item.http_res="{ item }" >
        <pre>{{ JSON.stringify(JSON.parse(item.http_res), null, 2) }}</pre>
      </template>
    </v-data-table>
  </v-container>
</template>

<script>
import axios from 'axios';

export default {
  name: 'Home',
  data () {
    return {
      tableHeaders: [
        {
          text: 'Date/Time',
          value: 'date_time',
          align: 'start',
          width: 170
        },
        { text: 'HTTP Response',
          value: 'http_res',
          sortable: false
        }
      ],
      tableData: [],
      isTableLoading: true
    }
  },
  methods: {
    getTableData() {
      axios.get('http://localhost:8082/api/httpRes')
        .then(response => {
          this.tableData = response.data;
          this.isTableLoading = false;
        })
        .catch(error => {
          console.error(error);
        });
    },
    addRow() {
      this.isTableLoading = true;
      axios.get('http://localhost:8081/datetime')
        .then(response => {
          return axios.post('http://localhost:8082/api/httpRes/store', {
            'date_time': response.data.date_time,
            'http_res': JSON.stringify(response)
          });
        })
        .then(() => {
          return this.getTableData();
        });
    }
  },
  created() {
    this.getTableData();
  }
}
</script>