/*
 * Copyright © Bold Brand Commerce Sp. z o.o. All rights reserved.
 * See LICENSE for license details.
 */
<template>
    <div class="date-picker-header">
        <slot name="previous">
            <Fab
                :theme="secondaryTheme"
                @click.native="onPrevious">
                <template #icon="{ color }">
                    <IconArrowSingle
                        :fill-color="color"
                        :state="leftArrow" />
                </template>
            </Fab>
        </slot>
        <span
            class="date-picker-header__title"
            @click="onClick"
            v-text="header" />
        <slot name="next">
            <Fab
                :theme="secondaryTheme"
                @click.native="onNext">
                <template #icon="{ color }">
                    <IconArrowSingle
                        :fill-color="color"
                        :state="rightArrow" />
                </template>
            </Fab>
        </slot>
    </div>
</template>

<script>
import { THEME } from '@Core/defaults/theme';
import { ARROW } from '@Core/defaults/icons';
import Fab from '@Core/components/Buttons/Fab';
import IconArrowSingle from '@Core/components/Icons/Arrows/IconArrowSingle';

export default {
    name: 'DatePickerNavigationHeader',
    components: {
        Fab,
        IconArrowSingle,
    },
    props: {
        header: {
            type: [String, Number],
            required: true,
        },
    },
    data() {
        return {
            leftArrow: ARROW.LEFT,
            rightArrow: ARROW.RIGHT,
        };
    },
    computed: {
        secondaryTheme() {
            return THEME.SECONDARY;
        },
    },
    methods: {
        onClick() {
            this.$emit('changeCalendarType');
        },
        onPrevious() {
            this.$emit('previousDate');
        },
        onNext() {
            this.$emit('nextDate');
        },
    },
};
</script>

<style lang="scss" scoped>
    .date-picker-header {
        display: flex;
        align-items: center;

        &__title {
            display: flex;
            flex: 1;
            justify-content: center;
            align-items: center;
            height: 32px;
            color: $GRAPHITE_DARK;
            font: $FONT_MEDIUM_14_20;
            cursor: pointer;

            &:hover {
                box-shadow: $ELEVATOR_2_DP;
            }
        }
    }
</style>
