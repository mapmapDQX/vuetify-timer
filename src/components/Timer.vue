<template>
  <v-container class="fill-height">
    <v-col>
      <v-row class="d-flex justify-center" align-content="center">
        <div class="pr-4" style="font-size: 30px">{{ formatTime }}</div>
        <v-btn height="40" style="font-size: 20px" color="primary" v-if="!timerOn" @click="start_timer">スタート</v-btn>
        <v-btn height="40" style="font-size: 20px" color="primary" v-if="timerOn" @click="stop_timer">ストップ</v-btn>
      </v-row>
      <v-row  class="d-flex justify-center">
        <v-table style="border-radius: 5px; font-size: 16px" density="compact">
          <template v-slot:default>
            <thead>
              <tr>
                <th class="text-left">残り時間</th>
                <th class="text-left">ジュエル</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(item, index) in items" :key="index">
                <td :style="{'background-color': getBGColor(item)}">{{ item.time }}</td>
                <td :style="{'background-color': getBGColor(item)}">{{ item.jewel }}</td>
              </tr>
            </tbody>
          </template>
        </v-table>
      </v-row>
    </v-col>
    <v-dialog width="auto" transition="dialog-transition" v-model="popup">
      <v-card style="background-color: black">
        <v-card-text>
          <p class="text-h5 text--primary">{{popup_item_time}}</p>
          <p class="text-h5 text--primary">{{popup_item_jewel}}</p>
        </v-card-text>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
  import audio from "../assets/決定ボタンを押す20.mp3"
  import time_4_55 from "../assets/time_4_55.mp3"
  import time_4_30 from "../assets/time_4_30.mp3"
  import time_4_20 from "../assets/time_4_20.mp3"
  import time_3_45 from "../assets/time_3_45.mp3"
  import time_3_30 from "../assets/time_3_30.mp3"
  import time_3_10 from "../assets/time_3_10.mp3"
  import time_2_35 from "../assets/time_2_35.mp3"
  import time_2_30 from "../assets/time_2_30.mp3"
  import time_2_00 from "../assets/time_2_00.mp3"
  import time_1_30 from "../assets/time_1_30.mp3"
  import time_1_25 from "../assets/time_1_25.mp3"
  import time_0_50 from "../assets/time_0_50.mp3"
  import time_0_30 from "../assets/time_0_30.mp3"
  export default {
    data () {
      return {
        sec: 330,
        timerOn: false,
        timerObj: null,
        items: [
          { time: "５：３０", jewel: "スタート", sec: 325, sound: audio, ontime: false },
          { time: "４：５５", jewel: "ジュエルラッシュ", sec: 295, sound: time_4_55, ontime: false },
          { time: "４：３０", jewel: "ボーナスジュエル (内周)", sec: 270, sound: time_4_30, ontime: false },
          { time: "４：２０", jewel: "ジュエルラッシュ", sec: 260, sound: time_4_20, ontime: false },
          { time: "３：４５", jewel: "ジュエルラッシュ", sec: 225, sound: time_3_45, ontime: false },
          { time: "３：３０", jewel: "ボーナスジュエル (外周)", sec: 210, sound: time_3_30, ontime: false },
          { time: "３：１０", jewel: "ジュエルラッシュ", sec: 190, sound: time_3_10, ontime: false },
          { time: "２：３５", jewel: "ジュエルラッシュ", sec: 155, sound: time_2_35, ontime: false },
          { time: "２：３０", jewel: "ボーナスジュエル (内周)", sec: 150, sound: time_2_30, ontime: false },
          { time: "２：００", jewel: "ジュエルラッシュ", sec: 120, sound: time_2_00, ontime: false },
          { time: "１：３０", jewel: "ボーナスジュエル (外周)", sec: 90, sound: time_1_30, ontime: false },
          { time: "１：２５", jewel: "ジュエルラッシュ", sec: 85, sound: time_1_25, ontime: false },
          { time: "０：５０", jewel: "ジュエルラッシュ", sec: 50, sound: time_0_50, ontime: false },
          { time: "０：３０", jewel: "ボーナスジュエル (内周)", sec: 30, sound: time_0_30, ontime: false }
        ],
        popup: false,
        popup_sec: 0,
        popup_item_time: "",
        popup_item_jewel: "",
      }
    },
    methods: {
      // タイマーカウント関数(1秒ごとに呼び出される)
      timer_count: function() {
        // ポップアップ表示処理
        if (this.popup_sec > 0) {
          this.popup_sec --;
          if (this.popup_sec <= 0) {
            this.popup = false;
          }
        }
        // ジュエルイベント処理
        for (let i = 0; i < this.items.length; i++) {
          // イベント5秒前にポップアップ表示と音声再生
          if (this.sec == this.items[i].sec + 5) {
            this.popup_item_time = this.items[i].time
            this.popup_item_jewel = this.items[i].jewel
            this.popup_sec = 5
            this.popup = true
            new Audio(this.items[i].sound).play()
          }
          // イベント時間になったら行の色を変える
          if (this.sec == this.items[i].sec) {
            this.items[i].ontime = true
          }
        }
        // タイマーカウント処理
        if (this.sec <= 0) {
          clearInterval(this.timerObj)
          this.timerOn = false
          this.sec = 330
          for (let i = 0; i < this.items.length; i++) {
            this.items[i].ontime = false
          }
        } else {
          this.sec --;
        }
      },

      start_timer: function() {
        let self = this;
        this.timerObj = setInterval(function() {self.timer_count()}, 1000)
        this.timerOn = true; //timerがONであることを状態として保持
      },

      stop_timer: function() {
        clearInterval(this.timerObj);
        this.timerOn = false; //timerがOFFであることを状態として保持
      },

      getBGColor(item) {
        return item.ontime ? '#606000' : 'dark'
      }
    },
    computed: {
      formatTime: function() {
        let timeStrings = [
          Math.floor(this.sec / 60).toString(),
          (this.sec % 60).toString()
        ].map(function(str) {
          if (str.length < 2) {
            return "0" + str
          } else {
            return str
          }
        })
        return timeStrings[0] + ":" + timeStrings[1]
      }
    }
  }
</script>

<style scoped>
</style>
