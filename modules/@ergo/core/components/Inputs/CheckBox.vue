/*
 * Copyright © Bold Brand Commerce Sp. z o.o. All rights reserved.
 * See LICENSE for license details.
 */

<template>
    <div class="checkbox">
        <input
            :id="associatedLabel"
            ref="checkbox"
            type="checkbox"
            :value="label"
            :disabled="disabled"
            v-model="checkValue">
        <label :for="associatedLabel">
            <svg
                class="checkbox__box"
                viewBox="0 0 14 10">
                <path
                    class="checkbox__mark"
                    :d="markDrawingCommands" />
            </svg>
            <span
                v-if="label"
                class="checkbox__label"
                v-text="label" />
        </label>
        <slot name="append" />
    </div>
</template>

<script>
import associatedLabelMixin from '@Core/mixins/inputs/associatedLabelMixin';

export default {
    name: 'CheckBox',
    mixins: [associatedLabelMixin],
    props: {
        value: {
            type: [Array, Boolean, Number],
            default: false,
        },
        disabled: {
            type: Boolean,
            default: false,
        },
        label: {
            type: String,
            default: '',
        },
    },
    computed: {
        checkValue: {
            get() {
                return this.value;
            },
            set(value) {
                this.$emit('input', value);
            },
        },
        markDrawingCommands() {
            return 'M5.22179 9.44443L0.777344 5.17093L2.02179 3.97435L5.22179 7.05127L11.9773 0.555542L13.2218 1.75212L5.22179 9.44443Z';
        },
    },
    watch: {
        value() {
            this.setIndeterminateState();
        },
    },
    mounted() {
        this.setIndeterminateState();
    },
    methods: {
        setIndeterminateState() {
            this.$refs.checkbox.indeterminate = this.value === 2;
        },
    },
};
</script>

<style lang="scss" scoped>
    .checkbox {
        $checkbox: &;

        position: relative;
        display: grid;
        grid-template-columns: max-content;
        grid-auto-flow: column;
        column-gap: 8px;
        align-items: center;
        margin: 4px;

        & label {
            position: relative;
            display: grid;
            grid-auto-flow: column;
            column-gap: 8px;
            align-items: center;
        }

        &__label {
            color: $GRAPHITE_DARK;
            font: $FONT_MEDIUM_12_16;
            cursor: pointer;
        }

        &__box {
            position: relative;
            display: flex;
            width: 16px;
            height: 16px;
            border: 1px solid $GREY;
            box-sizing: border-box;
            cursor: pointer;
        }

        &__mark {
            fill: none;
        }

        & input[type="checkbox"] {
            position: absolute;
            top: 0;
            left: 0;
            margin: 0;
            opacity: 0;

            &:checked:focus + label {
                #{$checkbox}__box {
                    box-shadow: $ELEVATOR_HOVER_FOCUS;
                }
            }

            &:not(:disabled) {
                &:checked + label, &:indeterminate + label {
                    #{$checkbox}__box {
                        background-color: $GREEN;
                        border-color: $GREEN;
                    }

                    #{$checkbox}__mark {
                        fill: $WHITE;
                    }
                }

                &:indeterminate + label {
                    #{$checkbox}__mark {
                        stroke: $WHITE;
                    }
                }
            }

            &:indeterminate + label {
                #{$checkbox}__mark {
                    d: path("M2,5 h10");
                    stroke-width: 2px;
                }
            }

            &:disabled {
                & + label {
                    #{$checkbox}__box {
                        background-color: $GREY_LIGHT;
                    }
                }

                &:checked + label {
                    #{$checkbox}__mark {
                        fill: $GREY_DARK;
                    }
                }

                &:indeterminate + label {
                    #{$checkbox}__mark {
                        stroke: $GREY_DARK;
                    }
                }
            }
        }
    }
</style>
