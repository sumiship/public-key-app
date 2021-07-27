<template>
  <div id="app">
    <div class="send-box">
      <Sender @message_update="message_update" :publick_key="public_key" />
    </div>
    <div class="route-box">
      <Route />
    </div>
    <div class="receive-box">
      <Receiver @key_update="key_update" :angou="message" />
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Sender from "./components/Sender.vue";
import Receiver from "./components/Receiver.vue";
import Route from "./components/Route.vue";

@Component({
  components: {
    Sender,
    Receiver,
    Route,
  },
})
export default class App extends Vue {
  private public_key: number[] = [];
  private message = "";

  private message_update(message: string) {
    this.message = message;
  }

  private key_update(key: number[]): void {
    this.public_key = key;
  }
}
</script>

<style lang="scss">
* {
  padding: 0;
  margin: 0%;
  box-sizing: border-box;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  box-sizing: border-box;
  text-align: center;
  color: #2c3e50;
  padding-top: 60px;
  display: flex;
  align-items: center;
  max-width: 1200px;
  width: 90%;
  margin: 0 auto;
  height: 100vh;
  & .send-box {
    width: 50%;
    height: 80%;
  }
  & .route-box {
    width: 15%;
  }
  & .receive-box {
    width: 50%;
    height: 80%;
  }
}
</style>
