<template>
  <div class="sender">
    <div class="sender__name">
      送信者
    </div>
    <div class="get-key">
      <div class="get-key__title">鍵取得</div>
      <br />
      <div class="get-key__key">{{ arr2string(key) }}</div>
      <div class="get-key__button" @click="getKey()">Get key</div>
    </div>
    <div class="generate-message">
      <div class="generate-message__title">メッセージ</div>
      <textarea
        class="generate-message__input"
        name="message"
        id=""
        v-model="message"
      ></textarea>
      <div class="generate-message__button" @click="conversion()">↓</div>
      <div class="generate-message__output">{{ angou }}</div>
      <div class="generate-message__send" @click="sendMessage()">送信</div>
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Vue, Prop, Emit } from "vue-property-decorator";

@Component({})
export default class App extends Vue {
  @Prop()
  publick_key!: number[];

  private message = "";
  private angou = "";
  private key: number[] = [];

  @Emit("message_update")
  sendMessage(): string {
    if (this.angou == "") alert("暗号文がありません");
    return this.angou;
  }

  private getKey(): void {
    if (this.publick_key.length == 0) {
      alert("公開鍵が作成されていません");
      return;
    }
    this.key = this.publick_key;
  }

  private arr2string(arr: number[]): string {
    let retString = "";
    arr.map((e) => (retString += String(e) + ","));
    return retString.slice(0, -1);
  }

  private bigintExpo(root: number, power: number): bigint {
    let retNumber = 1n;
    [...Array(power)].map(() => {
      retNumber *= BigInt(root);
    });
    return retNumber;
  }

  private conversion(): void {
    if (this.message == "") {
      alert("メッセージを作成してください");
      return;
    }
    if (this.key.length == 0) {
      alert("公開鍵を取得してください");
      return;
    }
    this.angou = "";
    [...Array(this.message.length)].map((_, i) => {
      this.angou += String.fromCharCode(
        Number(
          this.bigintExpo(this.message.charCodeAt(i), this.key[0]) %
            BigInt(this.key[1])
        ) + 33
      );
    });
  }
}
</script>
<style lang="scss" scoped>
.sender {
  height: 100%;
  background-color: rgb(226, 173, 154);
  &__name {
    font-size: 2.5rem;
  }
}
.get-key {
  width: 90%;
  margin: 5% auto 0;
  border: 1px solid rgb(14, 173, 221);
  border-radius: 8px;
  padding-bottom: 2%;
  &__title {
    display: inline-block;
    background-color: rgb(14, 173, 221);
    padding: 4px 5px;
    border-radius: 5px;
    transform: translateY(-50%);
    color: white;
  }
  &__key {
    display: inline-block;
    margin: 0 auto;
    padding: 3px 8px;
    background-color: aliceblue;
    border-radius: 12px;
  }
  &__button {
    font-size: 0.8rem;
    background-color: rgb(14, 173, 221);
    width: 30%;
    margin: 3% auto 0;
    padding: 5px 0;
    border-radius: 4px;
    color: white;
    cursor: pointer;
    margin-bottom: 2%;
    &:hover {
      box-shadow: 0 0 2px 2px rgb(207, 192, 205);
    }
  }
}
.generate-message {
  width: 90%;
  margin: 5% auto 0;
  border: 1px solid rgb(221, 14, 187);
  border-radius: 8px;
  padding-bottom: 2%;
  &__title {
    display: inline-block;
    background-color: rgb(221, 14, 187);
    padding: 4px 5px;
    border-radius: 5px;
    transform: translateY(-50%);
    color: white;
  }
  &__input {
    display: block;
    width: 90%;
    margin: 0 auto;
    height: 80px;
    font-size: 0.8rem;
  }
  &__button {
    font-size: 2rem;
    background-color: rgb(221, 14, 187);
    width: 10%;
    margin: 2% auto;
    padding-bottom: 5px;
    border-radius: 4px;
    color: white;
    cursor: pointer;
    margin-bottom: 2%;
    &:hover {
      box-shadow: 0 0 2px 2px rgb(206, 77, 184);
    }
  }
  &__output {
    background-color: rgb(255, 255, 255);
    width: 90%;
    text-align: start;
    margin: 0 auto;
    padding: 2%;
    word-break: break-all;
  }
  &__send {
    font-size: 0.8rem;
    background-color: rgb(221, 14, 187);
    width: 30%;
    margin: 3% auto 0;
    padding: 5px 0;
    border-radius: 4px;
    color: white;
    cursor: pointer;
    margin-bottom: 2%;
    &:hover {
      box-shadow: 0 0 2px 2px rgb(206, 77, 184);
    }
  }
}
</style>
