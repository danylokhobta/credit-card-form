<script>
import CardInput from './components/CardInput.vue'
import CardExpire from './components/CardExpire.vue'
import MenuComponent from './components/MenuComponent.vue'
import mastercardIcon from './assets/mastercard-icon.png'

export default {
  name: 'App',
  components: {
    CardInput,
    CardExpire,
    MenuComponent
  },
  data() {
    return {
      value: {
        number: 'XXXX XXXX XXXX XXXX',
        name: 'XXXXX XXXXX',
        month: 'XX',
        year: 'XX',
        cvv: 'XXX'
      },
      status: {
        number: false,
        name: false,
        expires: false,
        cvv: false
      },
      mastercardIcon: mastercardIcon,
      isCardFlipped: false
    }
  },
  methods: {
    handleValue(inputValue, id, status) {
      id === "number" && (
        this.value.number = inputValue,
        this.status.number = status
      );
      id === "name" && (
        inputValue === '' && (inputValue = 'XXXXX XXXXX'),
        this.value.name = inputValue,
        this.status.name = status
      )
      id === "month" && (
        this.value.month = inputValue,
        this.status.expires = status
      );
      id === "year" && (
        this.value.year = inputValue,
        this.status.expires = status
      );
      id === "cvv" && (
        this.value.cvv = inputValue,
        this.status.cvv = status
      );
    },
    flipCard() { this.isCardFlipped = !this.isCardFlipped; }, // Toggle card flip
    handleSubmit() {
      let finalStatus = true;
      for (let key in this.status) {
        if(this.status[key] !== true) {
          console.log(`This parameter isn't valid: ${key}`);
          return finalStatus = false;
        }
      }
      console.log("We did it! Uncork the champagne!🥳");
      finalStatus === true && this.$refs.myForm.submit();
    },
  }
}
</script>

<template>
  <div class="app">
    <MenuComponent />
    <div class="credit-card" @click="flipCard">
      <div class="card-inner" :class="{ 'is-flipped': isCardFlipped }">
        <div class="card-face card-front">
          <div class="card-info">
            <p class="logo">monobank</p>
            <p class="currency">UAH</p>
          </div>
          <div class="entered-data">
            <p class="card-number">{{ value.number }}</p>
            <p class="expire-date">{{ value.month }}/{{ value.year }}</p>
            <p class="holder-name">{{ value.name }}</p>
            <img :src="mastercardIcon" alt="Mastercard Icon">
          </div>
        </div>
        <div class="card-face card-back">
          <div class="mag-line"></div>
          <div class="cvv-container">
            <p class="cvv">{{ value.cvv }}</p>
          </div>
        </div>
      </div>
    </div>
    <form ref="myForm" @submit.prevent="handleSubmit">
      <CardInput 
        label="Card Number"
        type="number"
        id="number"
        :minlength="16"
        :maxlength="16"
        :spacing="4"
        @input-value="handleValue"
      />
      <CardInput
        label="Card Holder Name"
        type="string"
        id="name"
        :minlength="3"
        :maxlength="20"
        @input-value="handleValue"
      />
      <CardExpire
        label="Expires"
        @year-value="handleValue"
        @month-value="handleValue"
      />
      <CardInput
        label="CVV"
        type="number"
        id="cvv"
        :minlength="3"
        :maxlength="3"
        @input-value="handleValue"
      />
      <input type="submit" value="Submit" class="submit-btn">
    </form>
  </div>
</template>

<style lang="sass">
@import './components/CardStyle.sass'
@import './components/FormStyle.sass'
.app
  padding: 20px
  box-sizing: border-box
  display: flex
  flex-wrap: wrap
  justify-content: center
  align-items: center
  gap: 40px
@media (max-width: 500px)
  .app
    transform: scale(0.8)
</style>