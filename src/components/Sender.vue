<template>
  <div class="sender">
    <div class="sender__name">
      送信者
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Vue } from "vue-property-decorator";

@Component({})
export default class App extends Vue {}
</script>
<style lang="scss" scoped>
.sender {
  height: 100%;
  background-color: rgb(226, 173, 154);
  &__name {
    font-size: 25px;
  }
}
</style>
