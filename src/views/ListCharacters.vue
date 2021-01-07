<template>
  <div>
    <div
      v-if="loading"
    >loading...</div>
    <div v-if="!loading">
      <h2>List characters</h2>
      <ul>
        <li
        v-for="item in result.character"
        :key="item.id"
        >
          <span>
            <router-link :to="{ name: 'Character', params: { id: item.id }}">
              {{ item.name }}
            </router-link>
          </span>
          <span>{{ item.description }}</span>
        </li>
      </ul>
    </div>
    <code v-else>{{ result }}</code>
  </div>
</template>

<script>
import { useQuery } from "@vue/apollo-composable";
import gql from "graphql-tag";

export default {
  name: "ListCharacters",
  setup() {
    const { result, loading } = useQuery(gql`
      query MyQuery {
        character {
          id
          name
          description
        }
      }
    `);

    return { result, loading };
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
