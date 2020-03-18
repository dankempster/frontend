/*
 * Copyright © Bold Brand Commerce Sp. z o.o. All rights reserved.
 * See LICENSE for license details.
 */
<template>
    <Component
        :is="getComponentViaType"
        :solid="true"
        :required="true"
        :clearable="true"
        :small="true"
        :label="parameter.name"
        :options="conditionOptions"
        :value="conditionValue"
        :multiselect="isConditionTypeMultiSelect"
        :error-messages="errorParamsMessage"
        @input="setConditionValueByType" />
</template>
<script>
import { mapState, mapActions } from 'vuex';
import { TYPES } from '@Conditions/defaults/conditions';
import { isEmpty } from '@Core/models/objectWrapper';
import errorValidationMixin from '@Core/mixins/validations/errorValidationMixin';

export default {
    name: 'ConditionSetParameters',
    mixins: [errorValidationMixin],
    props: {
        parameter: {
            type: Object,
            required: true,
        },
        itemId: {
            type: String,
            required: true,
        },
        itemRow: {
            type: Number,
            required: true,
        },
    },
    computed: {
        ...mapState('conditions', {
            conditionsValues: state => state.conditionsValues,
        }),
        isConditionTypeMultiSelect() {
            return this.parameter.type === TYPES.MULTI_SELECT;
        },
        getComponentViaType() {
            switch (this.parameter.type) {
            case TYPES.SELECT:
            case TYPES.MULTI_SELECT:
                return () => import('@Core/components/Inputs/Select/TranslationSelect');
            case TYPES.TEXT:
            case TYPES.UNIT:
            case TYPES.NUMERIC:
                return () => import('@Core/components/Inputs/TextField');
            default:
                return null;
            }
        },
        conditionValue() {
            const { name } = this.parameter;

            if (!this.conditionsValues[this.itemId] || !this.conditionsValues[this.itemId][name]) {
                if (this.isConditionTypeMultiSelect) return [];
                return '';
            }

            return this.conditionsValues[this.itemId][name];
        },
        conditionOptions() {
            return this.parameter.options
                ? Object.keys(this.parameter.options).map(key => ({
                    id: key, key, value: this.parameter.options[key],
                }))
                : [];
        },
        errorParamsMessage() {
            const { name } = this.parameter;
            const parametersIndex = `conditions_element-${this.itemRow}`;
            return this.conditionParametersAreValidate(parametersIndex, name) || null;
        },
    },
    methods: {
        ...mapActions('conditions', [
            'setConditionValue',
        ]),
        setConditionValueByType(value) {
            const { name } = this.parameter;

            this.setConditionValue({
                conditionId: this.itemId,
                parameterName: name,
                parameterValue: value,
            });
        },
        conditionParametersAreValidate(index, key) {
            return !isEmpty(this.validationErrors)
            && this.validationErrors[index]
            && this.validationErrors[index][key]
                ? this.validationErrors[index][key].join(', ') : null;
        },
    },
};
</script>