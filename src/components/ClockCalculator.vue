<template>
    <div class="row">
      <div class="col-12">
        <h1>What Time?</h1>

        <div class="form-check form-check-inline" v-for="(time, index) in timeOptions" :key="index">
          <input class="form-check-input" type="radio" :value="time.value" v-model="selectedTimeOption">
          <label class="form-check-label">
             {{time.label}}
          </label>
        </div>

        <form class="clockset-container">
          <div class="row">
            <div class="col">
              <input type="number" class="form-control" v-model="clockSet.hour" :readOnly="selectedTimeOption == 'current_time'" :disabled="selectedTimeOption == 'current_time'">
            </div>
            <div class="col">
              <input type="number" class="form-control" v-model="clockSet.minute" :readOnly="selectedTimeOption == 'current_time'" :disabled="selectedTimeOption == 'current_time'">
            </div> 
            <div class="col">
             <select class="form-control"  v-model="clockSet.am_or_pm" :readOnly="selectedTimeOption == 'current_time'" :disabled="selectedTimeOption == 'current_time'">
                <option value="AM">AM</option>
                <option value="PM">PM</option>
              </select>
            </div>
          </div>
        </form>

        <form class="timeset-container">
          <div class="row">
            <div class="col">
              <input type="number" class="form-control" v-model="timeSet.hour" placeholder="Hours">
            </div>
            <div class="col">
              <input type="number" class="form-control" v-model="timeSet.minute" placeholder="Minutes">
            </div>
          </div>
        </form>

        <div class="form-check form-check-inline period-component" v-for="(time, index) in periodOptions"  :key="index">
          <input class="form-check-input" type="radio" :value="time.value" v-model="selectedPeriodOption">
          <label class="form-check-label">
             {{time.label}}
          </label>
        </div>

  

      </div>


      <div class="col-12 output-container">
        <h1>{{resultTime}}</h1>
      </div>

    </div>
</template>

<script>

import moment from 'moment';

export default {
  name: 'ClockCalculator',
  props: {},
  data() {
    return {
      selectedTimeOption: 'current_time',
      selectedPeriodOption: 'afterTime',
      continousSetTimeFn: '',
      clockSet: {
        hour: '00',
        minute: '00',
        am_or_pm: ''
      },
      timeSet: {
        hour: '',
        minute: ''
      },
      timeOptions: [
         { label: 'Time from now', value: 'current_time'},
         { label: 'Set initial time', value: 'custom_time'}
      ],
      periodOptions: [
         { label: 'Later from now', value: 'afterTime'},
         { label: 'Before from now', value: 'beforeTime'}
      ]
    }
  },
  computed: {
    resultTime() {

      if (this.timeSet.hour == '' && this.timeSet.minute == '') {
        return '00:00';
      }
  
      let tempClock = this.clockSet.hour + ':' + this.clockSet.minute + ' ' + this.clockSet.am_or_pm;
      let clockObj = moment(tempClock, 'h:mm A');


      if (this.selectedPeriodOption == 'afterTime') {
        clockObj = this.afterTime(clockObj);
      } else if (this.selectedPeriodOption == 'beforeTime') {
        clockObj = this.beforeTime(clockObj);
      }
       

      return clockObj.format('LT');
    }
  },
  watch: {
     selectedTimeOption() {
      this.setClock();
     }
  },
  methods: {
    afterTime(clock) {
        if (this.timeSet.minute !== '') {
          clock.add(this.timeSet.minute, 'minutes')
        }

        if (this.timeSet.hour !== '') {
           clock.add(this.timeSet.hour, 'hours')
        }

        return clock;
    },
    beforeTime(clock) {
        if (this.timeSet.minute !== '') {
          clock.subtract(this.timeSet.minute, 'minutes')
        }

        if (this.timeSet.hour !== '') {
           clock.subtract(this.timeSet.hour, 'hours')
        }

        return clock;
    },
    setCurrentClock() {
        let dt = moment();
        this.clockSet.hour = dt.format('h');
        this.clockSet.minute = dt.format('mm');
        this.clockSet.am_or_pm = dt.format('A');
    },
    setEmptyClock() {
        this.clockSet.hour = '00';
        this.clockSet.minute = '00';
    },
    setClock() {
       if (this.selectedTimeOption == 'current_time') {
            this.setCurrentClock();
            this.continousSetTimeFn = setInterval(() => {
                                    this.setCurrentClock();
                                 },  1000 );
       } else {
            this.setEmptyClock();
            clearInterval(this.continousSetTimeFn);
            this.continousSetTimeFn = '';
       }
    },
  },
  mounted() {
     this.setClock();
  }
}
</script>


<style scoped>
  .clockset-container, .timeset-container, .period-component {
    margin-top: 15px;
  }
  .output-container {
    margin-top: 20px;
  }
  .output-container h1 {
    font-size: 15vw;
  }
</style>
