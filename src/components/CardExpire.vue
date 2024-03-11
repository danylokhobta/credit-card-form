<template>
  <div class="input-container expires-container">
    <label>{{ label }}</label>
    <select
      :class="month.status !== undefined && (month.status ? 'success' : 'error')"
      v-model="month.answer"
      @change="emitExpirationMonth"
    >
      <!-- Loop to generate options of months -->
      <option
        v-for="month in month.options"
        :key="month"
        :value="month"
      >
        {{ month }}
      </option>
    </select>
    <select
      :class="year.status !== undefined && (year.status ? 'success' : 'error')"
      v-model="year.answer"
      @change="emitExpirationYear"
    >
      <!-- Loop to generate options of years -->
      <option
        v-for="year in year.options"
        :key="year"
        :value="year"
      >
        {{ year }}
      </option>
    </select>
  </div>
</template>

<script>
export default {
  name: 'CardExpire',
  props: { label: String },
  data (){
    return {
      month: {
        options: ['MM'],
        answer: 'MM',
        isMonthChosen: false,
        status: undefined
      },
      year: {
        options: ['YY'],
        answer: 'YY',
        isYearChosen: false,
        status: undefined
      },
      status: false
    }
  },
  emits: ['year-value', 'month-value'], // Declare the custom event here
  created() {
    // Function to add leading zero to single digit numbers
    const isTwoDigital = (val) => (val < 10) ? `0${val}` : val;
    // Generate options for months
    for(let i=1; i<=12; i++){
      this.month.options.push(isTwoDigital(i));
    }
    // Generate options for years
    let yearCount = 20; // Number of years to generate options for
    const lastTwoDigits = new Date().getFullYear() % 100; // Last two digits of current year
    for(let i=0; i<=yearCount; i++){
      if((i+lastTwoDigits)<100){
        this.year.options.push(isTwoDigital(i + lastTwoDigits));
      }
      else {
        let yearsLeft = yearCount - this.options.length + 1;
        for(let i=0; i<=yearsLeft; i++){
          this.year.options.push(isTwoDigital(i));
        }
        return;
      }
    }
  },
  methods: {
    statusSumUp() {
      const currentDate = new Date();
      const currentMonth = currentDate.getMonth() + 1; // Adding 1 because getMonth() returns zero-based index
      const currentYear = currentDate.getFullYear() % 100;

      if (this.month.answer <= currentMonth && this.year.answer === currentYear) {
        console.log("LOL, your credit card is expired...😑");
        this.status = this.month.status = this.year.status = false;
      } else if ((this.month.isMonthChosen && this.year.isYearChosen)) {
        console.log("Nice, your credit card is still valid👍");
        this.status = this.month.status = this.year.status = true;
      } else {
        this.status = false;
      }
    },
    emitExpirationMonth() {
      this.month.answer === 'MM' ?
        this.month.isMonthChosen = false :
        this.month.isMonthChosen = true;

      this.month.status = this.month.isMonthChosen;
      this.statusSumUp();
      this.$emit('month-value', this.month.answer, 'month', this.status);
    },
    emitExpirationYear() {
      this.year.answer === 'YY' ?
        this.year.isYearChosen = false :
        this.year.isYearChosen = true;

      this.year.status = this.year.isYearChosen;
      this.statusSumUp();
      this.$emit('year-value', this.year.answer, 'year', this.status);
    },
  }
}
</script>