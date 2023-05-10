<script setup>
  import { Authenticator } from "@aws-amplify/ui-vue";
  import "@aws-amplify/ui-vue/styles.css";

  import { Amplify } from 'aws-amplify';
  import awsconfig from './aws-exports';

  Amplify.configure(awsconfig);
</script>

<template>
  <div id="app">
    <authenticator>
    <template v-slot="{ user, signOut }">
      <div>
      Hello, {{ user.username }}.
      <button @click="signOut">Sign Out</button>
      </div>

      <div style="margin-top: 10px;">
      <h1>Todo App</h1>
      <input type="text" v-model="name" placeholder="Todo name" />
      <input type="text" v-model="description" placeholder="Todo description" />
      <button v-on:click="createTodo">Create Todo</button>
      <div v-for="item in todos" :key="item.id">
        <h3>{{ item.name }}</h3>
        <p>{{ item.description }}</p>
      </div>
      </div>
    </template>
    </authenticator>
  </div>
</template>

<script>
  import { API } from 'aws-amplify';
  import { createTodo } from './graphql/mutations';
  import { listTodos } from './graphql/queries';

  export default {
    name: 'App',
    async created() {
      this.getTodos();
    },
    data() {
      return {
        name: '',
        description: '',
        todos: []
      };
    },
    methods: {
      async createTodo() {
        const { name, description } = this;
        if (!name || !description) return;
        const todo = { name, description };
        this.todos = [...this.todos, todo];
        await API.graphql({
          query: createTodo,
          variables: { input: todo }
        });
        this.name = '';
        this.description = '';
      },
      async getTodos() {
        const todos = await API.graphql({
          query: listTodos
        });
        this.todos = todos.data.listTodos.items;
      }
    }
  };
</script>