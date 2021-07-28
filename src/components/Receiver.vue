<template>
  <div class="receiver">
    <div class="receiver__name">
      受信者
    </div>
    <div class="key-generate">
      <div class="key-generate__title">鍵生成</div>

      <div class="key-generate__input">
        <input type="number" placeholder="素数1" v-model="prime1" />
        <input type="number" placeholder="素数2" v-model="prime2" />
      </div>
      <div class="key-generate__button" @click="key_generate()">↓</div>
      <div class="key-generate__public">公開鍵：{{ public_key }}</div>
      <div class="key-generate__secret">秘密鍵：{{ secret_key }}</div>
    </div>
    <div class="receive-message">
      <div class="receive-message__title">受信BOX</div>
      <div class="receive-message__input">{{ angou }}</div>
      <div class="receive-message__button" @click="conversion()">↓</div>
      <div class="receive-message__output">{{ message }}</div>
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Vue, Watch, Emit, Prop } from "vue-property-decorator";

@Component({})
export default class App extends Vue {
  @Prop()
  angou!: string;

  private prime1 = "";
  private prime2 = "";
  private public_key: number[] = [];
  private secret_key: number[] = [];
  private message = "";

  @Watch("public_key")
  @Emit("key_update")
  key_update(): number[] {
    return this.public_key;
  }

  //ユーグリッド
  private eugrid(num1: number, num2: number) {
    while (num2) {
      [num1, num2] = [num2, num1 % num2];
    }
    return num1;
  }

  //互いに素
  private coprime(num: number) {
    let retNum = 0;
    while (!retNum) {
      let randomNum: number = Math.floor(Math.random() * num);
      if (randomNum == 0 || randomNum == 1) continue;
      if (this.eugrid(num, randomNum) == 1) retNum = randomNum;
    }
    return retNum;
  }

  //素数チェック
  private isPrime(num: number): boolean {
    if (num <= 1) return false;
    for (let i = 2; i <= Math.floor(Math.sqrt(num)); i++) {
      if (num % i == 0) return false;
    }
    return true;
  }

  //retNum * num1 % num2 = 1
  private remainder_one(num1: number, num2: number): number {
    let retNum = 0;
    while (retNum <= num2) {
      if ((retNum * num1) % num2 == 1) break;
      retNum++;
    }
    return retNum;
  }

  private bigintExpo(root: number, power: number): bigint {
    let retNumber = 1n;
    [...Array(power)].map(() => {
      retNumber *= BigInt(root);
    });
    return retNumber;
  }

  private key_generate() {
    if (
      this.prime1 == this.prime2 ||
      !this.isPrime(Number(this.prime1)) ||
      !this.isPrime(Number(this.prime2))
    ) {
      alert("2種類の素数を入力してください");
      this.prime1 = this.prime2 = "";
      this.secret_key = this.public_key = [];
      return;
    }
    const p = Number(this.prime1);
    const q = Number(this.prime2);
    const n: number = p * q;
    const f: number = (p - 1) * (q - 1);
    const k1 = this.coprime(f);
    const k2 = this.remainder_one(k1, f);
    this.public_key = [k1, n];
    this.secret_key = [k2, n];
  }

  private conversion(): void {
    if (this.angou == "") {
      alert("メッセージを受信してください");
      return;
    }
    this.message = "";
    [...Array(this.angou.length)].map((_, i) => {
      this.message += String.fromCharCode(
        Number(
          this.bigintExpo(this.angou.charCodeAt(i) - 33, this.secret_key[0]) %
            BigInt(this.secret_key[1])
        )
      );
    });
  }
}
</script>
<style lang="scss">
.receiver {
  height: 100%;
  background-color: rgb(227, 231, 165);
  &__name {
    font-size: 2.5rem;
  }
}
.key-generate {
  width: 90%;
  margin: 5% auto 0;
  border: solid 1px red;
  border-radius: 8px;
  &__title {
    display: inline-block;
    background-color: red;
    padding: 4px 5px;
    border-radius: 5px;
    transform: translateY(-50%);
    color: white;
  }
  &__input {
    justify-content: space-around;
    display: flex;
    & input {
      font-size: 1.3rem;
      width: 45%;
      margin-bottom: 2%;
    }
  }
  &__button {
    font-size: 2rem;
    background-color: red;
    // background-color: rgb(216, 172, 203);
    width: 10%;
    margin: 0 auto;
    padding-bottom: 5px;
    border-radius: 4px;
    color: white;
    cursor: pointer;
    margin-bottom: 2%;
    &:hover {
      box-shadow: 0 0 2px 2px rgb(207, 192, 205);
    }
  }
}

.receive-message {
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
    background-color: white;
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
}
</style>
