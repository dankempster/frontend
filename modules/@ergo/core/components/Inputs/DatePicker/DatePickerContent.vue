/*
 * Copyright © Bold Brand Commerce Sp. z o.o. All rights reserved.
 * See LICENSE for license details.
 */
<template>
    <div class="date-picker">
        <DatePickerHeader :header="header" />
        <DatePickerNavigationHeader
            :header="calendarHeader"
            @changeCalendarType="onChangeCalendarType"
            @previousDate="onPreviousDate"
            @nextDate="onNextDate" />
        <DatePickerCalendar
            :date="value"
            :month="month"
            :current-month="currentMonth"
            :year="year"
            :years="years"
            :current-year="currentYear"
            :calendar-type="selectedCalendarType"
            @input="onDateChange"
            @month="onMonthChange"
            @year="onYearChange"
            @calendarType="onCalendarTypeChange"
            @calendarHeader="onCalendarHeaderChange" />
    </div>
</template>

<script>
import DatePickerHeader from '@Core/components/Inputs/DatePicker/DatePickerHeader';
import DatePickerNavigationHeader from '@Core/components/Inputs/DatePicker/DatePickerNavigationHeader';
import DatePickerCalendar from '@Core/components/Inputs/DatePicker/Calendar/DatePickerCalendar';
import {
    getNextYear,
    getPreviousYear,
    getNextMonth,
    getPreviousMonth,
    getNextYearsRange,
    getPreviousYearsRange,
    getYearsWithinRange,
    getHeaderForCalendarDaysType,
    getHeaderForCalendarYearsType,
    zeroPad,
    CALENDAR_MONTHS,
} from '@Core/models/calendar/calendar';
import { CalendarType } from '@Core/models/calendar/CalendarType';

export default {
    name: 'DatePickerContent',
    components: {
        DatePickerHeader,
        DatePickerNavigationHeader,
        DatePickerCalendar,
    },
    props: {
        value: {
            type: Date,
            default: null,
        },
        calendarDate: {
            type: Date,
            default() {
                return new Date();
            },
        },
    },
    data() {
        const today = new Date();
        const year = this.calendarDate.getFullYear();
        const month = this.calendarDate.getMonth() + 1;

        return {
            years: getYearsWithinRange([], year),
            currentYear: today.getFullYear(),
            currentMonth: today.getMonth() + 1,
            month,
            year,
            selectedCalendarType: CalendarType.DAY,
            calendarHeader: getHeaderForCalendarDaysType(month, year),
        };
    },
    computed: {
        header() {
            if (!this.value) return 'Pick a date';

            const day = this.value.getDate();
            const month = this.value.getMonth();
            const year = this.value.getFullYear();

            switch (this.selectedCalendarType) {
            case CalendarType.DAY:
                return `${day}.${zeroPad(month, 2)}.${year}`;
            case CalendarType.MONTH: {
                const monthDesc = Object.values(CALENDAR_MONTHS)[month];
                return `${monthDesc} ${year}`;
            }
            case CalendarType.YEAR:
                return year;
            default:
                return '';
            }
        },
    },
    methods: {
        onDateChange(value) {
            this.$emit('input', value);
        },
        onMonthChange(month) {
            this.month = month;
        },
        onYearChange(year) {
            this.year = year;
        },
        onCalendarTypeChange(type) {
            this.selectedCalendarType = type;
        },
        onCalendarHeaderChange(header) {
            this.calendarHeader = header;
        },
        onPreviousDate() {
            switch (this.selectedCalendarType) {
            case CalendarType.DAY: {
                const {
                    month: previousMonth, year: previousYear,
                } = getPreviousMonth(this.month, this.year);

                this.month = previousMonth;
                this.year = previousYear;
                this.calendarHeader = getHeaderForCalendarDaysType(previousMonth, previousYear);

                break;
            }
            case CalendarType.MONTH:
                this.year = getPreviousYear(this.year);
                this.calendarHeader = this.year;

                break;
            case CalendarType.YEAR:
                this.years = getPreviousYearsRange(this.years);
                this.calendarHeader = getHeaderForCalendarYearsType(this.years);

                break;
            default:
                break;
            }
        },
        onNextDate() {
            switch (this.selectedCalendarType) {
            case CalendarType.DAY: {
                const {
                    month: nextMonth, year: nextYear,
                } = getNextMonth(this.month, this.year);

                this.month = nextMonth;
                this.year = nextYear;
                this.calendarHeader = getHeaderForCalendarDaysType(nextMonth, nextYear);

                break;
            }
            case CalendarType.MONTH: {
                this.year = getNextYear(this.year);
                this.calendarHeader = this.year;

                break;
            }
            case CalendarType.YEAR:
                this.years = getNextYearsRange(this.years);
                this.calendarHeader = getHeaderForCalendarYearsType(this.years);

                break;
            default:
                break;
            }
        },
        onChangeCalendarType() {
            switch (this.selectedCalendarType) {
            case CalendarType.DAY:
                this.selectedCalendarType = CalendarType.MONTH;
                this.calendarHeader = this.year;
                break;
            case CalendarType.MONTH:
                this.selectedCalendarType = CalendarType.YEAR;
                this.calendarHeader = getHeaderForCalendarYearsType(this.years);
                break;
            case CalendarType.YEAR:
                this.selectedCalendarType = CalendarType.DAY;
                this.calendarHeader = getHeaderForCalendarDaysType(this.month, this.year);
                break;
            default: break;
            }
        },
    },
};
</script>

<style lang="scss" scoped>
    .date-picker {
        width: 224px;
        padding: 16px;
    }
</style>
