<template>
  <div id="app">
    <h1>GitHub explorer</h1>

    <div v-if="selected.id">
      <div v-for="item in results" v-if="item.id === selected.id">
        <h2>Details</h2>
        <div>Score: {{ item.score }}</div>
        <div>Forks?: {{ item.fork ? 'true' : 'false' }}</div>
        <div>Full Name: {{ item.full_name }}</div>
        <button type="button" name="button" @click="cancel">Hide</button>
      </div>
    </div>

    <div v-else>
      <div>
        <input type="text" v-model="searchText" @input="onChange" />
        <select v-model="searchBy" @change="onChange">
          <option value="repositories">repositories</option>
          <option value="users">users</option>
          <option value="issues">issues</option>
        </select>
      </div>
      <ul>
        <li v-for="item in results" @click="select(item.id)">
          <div>{{ item.name }}</div>
          <a :href="item.url">{{ item.url }}</a>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'app',
  data() {
    return {
      searchBy: 'repositories',
      searchText: '',
      results: [],
      selected: {},
    };
  },
  methods: {
    search(byRepo, byUser, byIssues, text) {
      if (byRepo) {
        return axios.get(`https://api.github.com/search/repositories?q=${text}`);
      }
      if (byUser) {
        return axios.get(`https://api.github.com/search/users?q=${text}`);
      }
      if (byIssues) {
        return axios.get(`https://api.github.com/search/issues?q=${text}`);
      }
    },
    onChange() {
      this.search(
        this.searchBy === 'repositories',
        this.searchBy === 'users',
        this.searchBy === 'issues',
        this.searchText,
      ).then((res) => {
        this.results = res.data.items;
      });
    },
    select(id) {
      this.selected.id = id;
    },
    cancel() {
      this.selected.id = null;
    },
  },
};
</script>
