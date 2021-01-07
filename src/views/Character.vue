<template>
  <div>
    <div
      v-if="result
        && result.character
        && result.character.length > 0
        && result.character[0]"
    >
      {{ state.audioObject }}
      <h3>{{ result.character[0].name }}</h3>
      <div>
        <span
          v-for="symbol in ['a', 'b', 'c', 'd', 'e']"
          :key="symbol"
          @click="play(
            state.symbolsWithAudio[symbol]
          )"
          class='letter'
        >
          {{ state.symbolsWithAudio[symbol] && state.symbolsWithAudio[symbol].fileName }}
          {{ symbol }}
        </span>  
      </div>
    </div>
    <div>id: {{ state.id }}</div>
    <div v-if="loading">loading...</div>
    <code v-else>{{ result }}</code>
  </div>
</template>

<script>
import { useQuery } from "@vue/apollo-composable";
import { reactive, computed } from '@vue/composition-api'
import gql from "graphql-tag";
import { useRoute } from 'vue-router'

export default {
  name: "Character",
  setup() {
    const route = useRoute()
    const { result, loading } = useQuery(gql`
      query MyQuery ($id: Int!) {
        character (where: { id: { _eq: $id} }){
          id
          name
          description
          audios {
            id
            fileName
            begin
            end
            symbol
            type
          }
        }
    }
    `, {"id": state.id});
    // watch create object {a: {fileName, begin, end}}    

    let state = reactive({
      audioObject: null,
      id: computed(() => route && route.params && route.params.id),
      symbolsWithAudio: computed(() => {
      const obj = {};
      let audios = null;
      if (
        result
        && result.value
        && result.value.character
        && (result.value.character.length > 0)
        && result.value.character[0].audios
      ) {
        audios = result.value.character[0].audios;
      }
      if (audios) {
        audios.forEach((audio) => {
          obj[audio.symbol] = audio;
        })
      }
      return obj;
    })
    });
    

    function play(obj) {
      let fileName = null;
      let begin = null;
      let end = null;
      if (obj) {
        fileName = obj.fileName;
        begin = obj.begin;
        end = obj.end;
      }
      if (fileName && begin && end) {
        if (state.audioObject) {
          state.audioObject.pause();
        }
        state.audioObject = new Audio(`/audio/${fileName}.mp3`);
        state.audioObject.currentTime = begin;
        setTimeout(function() {
          state.audioObject.play();
        }, 0);
        state.audioObject.addEventListener('timeupdate', () => {
          console.log(state.audioObject.currentTime);
          if (state.audioObject.currentTime >= end) {
            state.audioObject.pause();
          }
        });
      }
    }
    return { state, result, loading, play };
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
.letter {
  margin: 20px;
}
</style>
