<template>
    <div>
        <div id="header">
            <div>
                <h1>Vue.js Calendar</h1>
            </div>
            <div>
                <current-month></current-month>
            </div>
        </div>
        <div id="day-bar">
            <div>Sun</div>
            <div>Mon</div>
            <div>Tues</div>
            <div>Wed</div>
            <div>Thurs</div>
            <div>Fri</div>
            <div>Sat</div>
        </div>
        <div id="calendar">
            <div v-for="week in weeks" class="calendar-week">
                <CalendarDay v-for="day in week" :day="day"></CalendarDay>
            </div>

        </div>
        <event-form></event-form>
    </div>

</template>

<script>
    import CalendarDay from './CalendarDay.vue'
    import CurrentMonth from './CurrentMonth.vue'
    import EventForm from "./EventForm.vue";

    export default {

        computed: {
            month () {
                return this.$store.state.currentMonth;
            },
            year () {
                return this.$store.state.currentYear;
            },
            days () {

                // generating all days in current month
                let days = [];
                let currentDay = this.$moment(`${this.year}-${this.month}-1`, 'YYYY-M-D');
                do {
                    days.push(currentDay);
                    currentDay = this.$moment(currentDay).add(1, 'days');
                } while ((currentDay.month() + 1 ) === this.month);

                // Add previous days to start
                currentDay = this.$moment(days[0]);
                const FRIDAY = 5;
                const SATURDAY = 6;
                const MONDAY = 1;

                if (currentDay.day() !== SATURDAY) {
                    do {
                        currentDay = this.$moment(currentDay).subtract(1, 'days');
                        days.unshift(currentDay);
                    } while(currentDay.day() !== SATURDAY)
                }

                // add days on end of month to fill up calendar spaces
                currentDay = this.$moment(days[days.length - 1]);

                if (currentDay.day() !== FRIDAY) {
                    do {
                        currentDay = this.$moment(currentDay).add(1, 'days');
                        days.push(currentDay);
                    } while (currentDay.day() !== FRIDAY)
                }

                return days
            },
            weeks () {
                let weeks = [];
                let week = [];

                for (let day of this.days) {
                    week.push(day);
                    if (week.length === 7) {
                        weeks.push(week);
                        week = [];
                    }
                }

                return weeks
            }
        },
        components: {
            EventForm,
            CalendarDay,
            CurrentMonth
        }
    }
</script>

<style scoped>

</style>
