<template>
  <div class="container" style="display: grid; place-items: center">
    <v-dialog v-model="dialog" persistent max-width="400px">
      <v-card>
        <v-card-title>
          <span class="text-h5">Enter your name</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12" sm="6" md="4">
                <v-text-field
                  v-model="user.name"
                  label="username"
                  required
                ></v-text-field>
              </v-col>
            </v-row>
          </v-container>
          <!-- <small>*indicates required field</small> -->
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="dialog = false">
            Close
          </v-btn>
          <v-btn
            color="blue darken-1"
            text
            @click="(dialog = false), gameStart()"
          >
            confirm
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <div class="row">
      <div class="col-sm-2">
        <v-btn class="userChange" text @click="dialog = true">USER</v-btn>
        <br />
        : {{ user.name }}
        <v-spacer></v-spacer>
        <br />
        click:
        <br />
        {{ clicked }}
        <br />
        My Best:
        <br />
        {{ mybest }}
        <br />
        Global Best:
        <br />
        {{ globalbest }}
        <br />

        <v-btn dark rounded @click="gameStart()"> NEW GAME </v-btn>
      </div>
      <div class="row col-sm-10">
        <div v-for="(item, index) in items" :key="item.id" class="col-sm-3">
          <div
            :class="
              item.correct === 1
                ? 'cardCorrect'
                : item.status === 1
                ? 'cardOpen'
                : 'cardClose'
            "
            @click="
              target.length === 2 ? log('') : (item.status = 1);
              clicked++;
              target.push(index);
            "
          >
            <h1 v-if="item.status === 1 || item.correct === 1">
              {{ item.num }}
            </h1>
          </div>
        </div>
      </div>
    </div>
    <h1 v-if="counter === 6">YOU WIN!!!</h1>
  </div>
</template>

<script>
export default {
  data() {
    return {
      user: {
        name: "",
        score: 0,
      },
      target: [],
      items: [],
      counter: 0,
      clicked: 0,
      mybest: "-",
      globalbest: "-",
      dialog: true,
    };
  },
  watch: {
    counter(newValue) {
      if (newValue === 6) {
        if (this.clicked < this.mybest || this.mybest === "-") {
          this.mybest = this.clicked;
        }
      }
    },
    target() {
      if (this.target.length === 2) {
        console.log(this.target);
        if (this.target[0] === this.target[1]) {
          this.target.pop();
        } else {
          this.calculate();
        }
      }
    },
  },
  created() {
    this.dialog = true;
    this.gameStart();
  },
  methods: {
    gameStart() {
      this.counter = 0;
      this.clicked = 0;
      this.target = [];
      this.items = [];
      for (let index = 1; index < 7; index++) {
        this.items.push({
          num: index,
          status: 0,
          correct: 0,
        });
        this.items.push({
          num: index,
          status: 0,
          correct: 0,
        });
      }
      for (var i = this.items.length - 1; i > 0; i--) {
        var j = Math.floor(Math.random() * (i + 1));
        var temp = this.items[i];
        this.items[i] = this.items[j];
        this.items[j] = temp;
      }
    },
    calculate() {
      if (this.items[this.target[0]].num === this.items[this.target[1]].num) {
        this.items[this.target[0]].correct = 1;
        this.items[this.target[1]].correct = 1;
        this.target = [];
        this.counter++;
      } else {
        var delayInMilliseconds = 400; // 1 second
        var boo = this;
        setTimeout(function () {
          boo.items[boo.target[0]].status = 0;
          boo.items[boo.target[1]].status = 0;
          boo.target = [];
        }, delayInMilliseconds);
      }
    },
  },
};
</script>

<style>
.cardClose {
  width: 100px;
  height: 100px;
  background-color: cadetblue;
  display: grid;
  place-items: center;
}
.cardOpen {
  width: 100px;
  height: 100px;
  background-color: #bbbbbb;
  display: grid;
  place-items: center;
}
.cardCorrect {
  width: 100px;
  height: 100px;
  background-color: limegreen;
  display: grid;
  place-items: center;
}
.userChange {
  width: 25px;
  height: 30px;
  background-color: rgb(178, 214, 77);
  place-items: left;
}
</style>
