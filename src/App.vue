<template>
  <div>
    <div>
      <select @change="changeLanguage" v-model="language">
        <option value="en">English</option>
        <option value="ru">Russian</option>
      </select>
    </div>
    <div class="calendar-header">
      <button @click="prevMonth">{{ '<' }}</button>
      <span>{{ currentMonthName }} {{ currentYear }}</span>
      <button @click="nextMonth">{{ '>' }}</button>
    </div>
    <table class="calendar">
      <thead>
        <tr>
          <th v-for="day in days" :key="day">{{ day }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(week, index) in calendar" :key="index">
          <td
            v-for="(day, index) in week"
            :key="index"
            @click="selectDate(day)"
            :class="{ 'selected': isDaySelected(day) }"
          >
            {{ day }}
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  props: ['date'],
  data() {
    return {
      days: [],
      calendar: [],
      currentYear: 0,
      currentMonth: '',
      selectedDate: null,
      language: 'en',
      monthNames: {
        en: [
          'January',
          'February',
          'March',
          'April',
          'May',
          'June',
          'July',
          'August',
          'September',
          'October',
          'November',
          'December'
        ],
        ru: [
          'Январь',
          'Февраль',
          'Март',
          'Апрель',
          'Май',
          'Июнь',
          'Июль',
          'Август',
          'Сентябрь',
          'Октябрь',
          'Ноябрь',
          'Декабрь'
        ]
      },
      dayNames: {
        en: ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'],
        ru: ['Вс', 'Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб']
      }
    }
  },
  mounted() {
    const today = this.date ? new Date(this.date) : new Date()
    this.currentYear = today.getFullYear()
    this.currentMonth = today.getMonth()
    this.selectDate(today.getDate())
    this.generateCalendar()
  },
  methods: {
    generateCalendar() {
      const firstDayOfMonth = new Date(this.currentYear, this.currentMonth, 1).getDay()
      const daysInMonth = new Date(this.currentYear, this.currentMonth + 1, 0).getDate()
      const prevMonthDays = new Date(this.currentYear, this.currentMonth, 0).getDate()

      this.days = this.dayNames[this.language]

      let calendar = []
      let week = []

      for (let i = firstDayOfMonth; i > 0; i--) {
        week.push(prevMonthDays - i + 1)
      }

      for (let day = 1; day <= daysInMonth; day++) {
        week.push(day)
        if (week.length === 7) {
          calendar.push(week)
          week = []
        }
      }

      while (week.length < 7) {
        week.push('')
      }

      calendar.push(week)

      this.calendar = calendar
    },
    prevMonth() {
      if (this.currentMonth === 0) {
        this.currentMonth = 11
        this.currentYear--
      } else {
        this.currentMonth--
      }
      this.generateCalendar()
    },
    nextMonth() {
      if (this.currentMonth === 11) {
        this.currentMonth = 0
        this.currentYear++
      } else {
        this.currentMonth++
      }
      this.generateCalendar()
    },
    selectDate(day) {
      if (day !== '') {
        this.selectedDate = {
          day,
          month: this.currentMonth,
          year: this.currentYear
        }
      }
    },
    changeLanguage() {
      this.days = this.dayNames[this.language]
      this.generateCalendar()
    },
    isDaySelected(day) {
      if (
        this.selectedDate.day === day &&
        this.selectedDate.month === this.currentMonth &&
        this.selectedDate.year === this.currentYear
      ) {
        return true
      }
      return false
    }
  },
  computed: {
    currentMonthName() {
      return this.monthNames[this.language][this.currentMonth]
    }
  }
}
</script>

<style scoped>
.calendar {
  border-collapse: collapse;
}
.calendar th,
.calendar td {
  border: 1px solid #ccc;
  padding: 5px;
  text-align: center;
}
.calendar-header {
  display: flex;
  justify-content: space-between;
}
.selected {
  background-color: #3498db;
  color: #fff;
}
</style>
