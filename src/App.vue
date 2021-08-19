<template>
  <div class="container mx-auto py-12 volk">
    <div class="main-text">Crypto Exchange</div>
    <div class="sup-text">Exchange fast and easy</div>
    <div class="flex justify-between main-content">
      <!-- left -->
      <div class="mt-8 mb-8 relative flex justify-between allInput price">
        <input
          type="text"
          name="price"
          id="price_left"
          class="cur_input bg_col"
          placeholder="0.00"
          v-on:change="setLeft"
        />
        <div class="flex items-center cur_container">
          <img class="-m-l-20" :src="defImgLeft" alt="" />
          <input
            class="dropD bg_col cur_container"
            id="currency_left"
            :value="defLeft.toUpperCase()"
            v-on:click="onFocusLeft"
          />
          <!-- DropDown -->
          <ul
            v-if="dropDown_left"
            id="ddLeft"
            class="absolute ul_test left_coin_ui"
          >
            <li>
              <input
                class="cur_input_drop"
                type="text"
                id="left_coin_search"
                value=""
                v-on:change="searchLeft"
                placeholder="Search"
              />
            </li>
            <li class="li_drop" v-for="c in coinsLeft" :key="c.name">
              <a href="#" v-on:click="liClickLeft(c)" class="a-w">
                <div class="flex justify-content">
                  <img class="li_img" :src="c.image" alt="" />
                  <p class="cur_ticker">{{ c.ticker.toUpperCase() }}</p>
                  <p class="cur_name">{{ c.name }}</p>
                </div>
              </a>
            </li>
          </ul>
        </div>
      </div>

      <!-- button -->
      <button class="mt-8 mb-8 swap_btn">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          class="h-5 w-5"
          viewBox="0 0 20 20"
          fill="currentColor"
        >
          <path
            d="M8 5a1 1 0 100 2h5.586l-1.293 1.293a1 1 0 001.414 1.414l3-3a1 1 0 000-1.414l-3-3a1 1 0 10-1.414 1.414L13.586 5H8zM12 15a1 1 0 100-2H6.414l1.293-1.293a1 1 0 10-1.414-1.414l-3 3a1 1 0 000 1.414l3 3a1 1 0 001.414-1.414L6.414 15H12z"
          />
        </svg>
      </button>
      <!-- right -->
      <div class="mt-8 mb-8 relative flex justify-between allInput price">
        <input
          type="text"
          name="price"
          id="price_right"
          class="cur_input bg_col"
          placeholder="0.00"
        />
        <div class="flex items-center cur_container">
          <img class="-m-l-20" :src="defImgRight" alt="" />
          <input
            class="dropD bg_col cur_container"
            :value="defRight.toUpperCase()"
            v-on:click="onFocusRight"
            id="currency_right"
          />
          <!-- DropDown -->
          <ul
            v-if="dropDown_right"
            id="ddRight"
            class="absolute ul_test left_coin_ui"
          >
            <li>
              <input
                class="cur_input_drop"
                type="text"
                id="right_coin_search"
                value=""
                v-on:change="Search"
                placeholder="Search"
              />
            </li>
            <li class="li_drop" v-for="c in coinsRight" :key="c.name">
              <a href="#" v-on:click="liClickRight(c)" class="a-w">
                <div class="flex justify-content">
                  <img class="li_img" :src="c.image" alt="" />
                  <p class="cur_ticker">{{ c.ticker.toUpperCase() }}</p>
                  <p class="cur_name">{{ c.name }}</p>
                </div>
              </a>
            </li>
          </ul>
        </div>
      </div>
    </div>
    <!-- низ -->
    <div>Your Ethereum address</div>
    <div class="flex justify-content btn_exchange_container">
      <input
        class="mr-10 bot_input allInput"
        name="test"
        type="text"
        id="test"
        value=""
      />
      <div>
        <button class="btn_exchange" id="exch" v-on:click="Exchange">
          {{"Exchange".toUpperCase()}}
        </button>
        <p class="alert-text" v-if="pairDisabled">This pair is disabled now</p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data: () => ({
    dropDown_left: false,
    dropDown_right: false,
    pairDisabled: false,
    defLeft: "btc",
    defImgLeft: "https://changenow.io/images/sprite/currencies/btc.svg",
    defRight: "btc",
    defImgRight: "https://changenow.io/images/sprite/currencies/btc.svg",
    selectedCoin: "",
    coins: [],
    coinsLeft: [],
    coinsRight: [],
    errors: [],
    minExchangeAmount: {
      minAmount: 0,
    },
    exchangeAmount: {
      estimatedAmount: 0,
    },
  }),

  created() {
    axios
      .get("https://api.changenow.io/v1/currencies?active=true")
      .then((response) => {
        this.coins = response.data;
        this.coinsLeft = response.data;
        this.coinsRight = response.data;
      })
      .catch((e) => {
        this.errors.push(e);
      });
  },

  computed: {},

  methods: {
    itemImage() {
      console.log(this.selectedCoin);
      return this.selectedCoin.image;
    },

    onCurChange() {
      var prL = document.getElementById("price_left");
      var apiKey =
        "c9155859d90d239f909d2906233816b26cd8cf5ede44702d422667672b58b0cd";

      axios
        .get(
          `https://api.changenow.io/v1/min-amount/${this.defLeft}_${this.defRight}?api_key=${apiKey}`
        )
        .then((response) => {
          if (response.data.minAmount == null) {
            this.pairDisabled = true;
          }
          prL.value = response.data.minAmount;
          this.minExchangeAmount = response.data.minAmount;
          this.Exchange();
        })
        .catch((e) => {
          this.errors.push(e);
        });
    },

    Exchange() {
      var prL = document.getElementById("price_left");
      var prR = document.getElementById("price_right");
      var apiKey =
        "c9155859d90d239f909d2906233816b26cd8cf5ede44702d422667672b58b0cd";

      axios
        .get(
          `https://api.changenow.io/v1/exchange-amount/${prL.value}/${this.defLeft}_${this.defRight}?api_key=${apiKey}`
        )
        .then((response) => {
          if (response.data.estimatedAmount == null) {
            this.pairDisabled = true;
          }
          prR.value = response.data.estimatedAmount;
          console.log("exchange-amount", response.data);
        })
        .catch((e) => {
          this.errors.push(e);
        });
    },

    setLeft() {
      var prL = document.getElementById("price_left");
      var prR = document.getElementById("price_right");
      if (prL.value < this.minExchangeAmount) {
        prR.value = "-";
      }
      console.log(this.minExchangeAmount);
    },

    searchLeft() {
      var c = this.coins;
      var el = document.getElementById("left_coin_search");
      var arrr = [];
      var news = el.value.toLowerCase();

      c.forEach((coin) => {
        if (coin.ticker.includes(news)) {
          arrr.push(coin);
        }
      });

      this.coinsLeft = arrr;

      return this.coinsLeft;
    },

    searchRight() {
      var c = this.coins;
      var el = document.getElementById("right_coin_search");
      var arrr = [];
      var news = el.value.toLowerCase();

      c.forEach((coin) => {
        if (coin.ticker.includes(news)) {
          arrr.push(coin);
        }
      });

      this.coinsRight = arrr;

      return this.coinsRight;
    },

    onFocusLeft() {
      this.dropDown_left = !this.dropDown_left;
    },

    onFocusRight() {
      this.dropDown_right = !this.dropDown_right;
    },

    liClickLeft(c) {
      var el = document.getElementById("ddLeft");
      this.defLeft = c.ticker;
      this.defImgLeft = c.image;
      el.classList.add("hidden");
      this.onCurChange();
    },

    liClickRight(c) {
      var el = document.getElementById("ddRight");
      this.defRight = c.ticker;
      this.defImgRight = c.image;
      el.classList.add("hidden");
      this.onCurChange();
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Vollkorn&display=swap");

.bot_input {
  width: 723px;
  height: 50px;
}

.-m-l-20 {
  margin-left: -30px;
}

.btn_exchange {
  width: 205px;
  height: 50px;
  background-color: #11b3fe;
  border-radius: 5px;
  color: #ffffff;
}

.price {
  height: 50px;
  width: 440px;
}

.cur_container {
  width: 150px;
  margin-left: 10px;
}

.alert-text {
  font-family: Roboto;
  font-style: normal;
  font-weight: normal;
  font-size: 16px;
  line-height: 23px;

  /* identical to box height, or 144% */
  display: flex;
  align-items: center;

  color: #e03f3f;
}

.li_drop {
  height: 48px;
  width: 438px;
  display: flex;
  margin: auto;
  align-items: center;
}

.li_img {
  margin: 0 10px;
}

.cur_input {
  width: 290px;
}

.cur_ticker {
  font-size: 16px;
  font-style: normal;
  font-weight: 400;
  line-height: 23px;
  letter-spacing: 0em;
  text-align: left;
}

.cur_name {
  margin-left: 16px;
  font-style: normal;
  font-weight: normal;
  font-size: 16px;
  line-height: 23px;
  display: flex;
  align-items: center;
  color: #80a2b6;
}

.a-w {
  width: 100%;
}

.li_drop:hover {
  background: #eaf1f7;
}

.cur_input_drop {
  width: 440px;
  height: 48px;
  border-bottom: 1px solid #e3ebef;
  background-color: #f6f7f8 !important;
  border-radius: 5px;
}

.bg_col {
  background-color: #f6f7f8 !important;
}

.allInput {
  background-color: #f6f7f8 !important;
  border-radius: 5px;
  border: 1px solid #e3ebef;
  box-sizing: border-box;
}

.volk {
  font-family: "Vollkorn", serif;
  width: 940px;
}

.left_coin_ui {
  top: -25px;
}

.ul_test {
  margin-top: 25px;
  margin-left: -290px;
  position: absolute;
  width: 440px;
  height: 194px;
  overflow: auto;
  background-color: #f6f7f8;
}

.main-text {
  font-style: normal;
  font-weight: 300;
  font-size: 50px;
  line-height: 120%;

  /* identical to box height, or 60px */
  display: flex;
  align-items: center;

  color: #282828;
}

@media screen and (max-width: 450px) {
  .main-text {
    font-size: 40px;
  }

  .cur_input {
    width: 178px;
  }

  .dropD {
    width: 140px;
  }

  .main-content {
    display: block !important;
  }

  .allInput {
    margin: 10px 0 !important;
    width: 328px;
  }

  .swap_btn {
    margin: 10px 10px 20px 10px !important;
    float: right;
  }

  .btn_exchange {
    width: 328px;
    height: 50px;
  }

  .btn_exchange_container {
    display: block !important;
  }

  .container {
    margin: 0 16px !important;
  }

  .volk {
    width: auto;
  }

  .ul_test {
    margin-top: 23px !important;
    margin-left: -190px !important;
    width: 328px;
    height: 100px;
  }
}

.sup-text {
  font-style: normal;
  font-weight: normal;
  font-size: 20px;
  line-height: 100%;

  /* identical to box height, or 20px */
  display: flex;
  align-items: center;

  color: #282828;
}
</style>
