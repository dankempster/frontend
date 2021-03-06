/*
 * Copyright © Bold Brand Commerce Sp. z o.o. All rights reserved.
 * See LICENSE for license details.
 */
<template>
    <ProductTemplateFormField
        :size="size"
        :position="position">
        <FormValidatorField :field-key="fieldKey">
            <template #validator="{ errorMessages }">
                <DatePicker
                    :value="fieldData"
                    solid
                    regular
                    :label="label"
                    :placeholder="properties.placeholder"
                    :foramt="parameter"
                    :error-messages="errorMessages"
                    :required="properties.required"
                    :disabled="disabled"
                    :description="properties.hint"
                    @focus="onFocus"
                    @input="onValueChange">
                    <template #append>
                        <TextFieldSuffix
                            v-if="parameter"
                            :suffix="parameter" />
                    </template>
                    <template #informationLabel>
                        <div />
                    </template>
                </DatePicker>
            </template>
        </FormValidatorField>
    </ProductTemplateFormField>
</template>

<script>
import { mapState, mapActions } from 'vuex';
import { format as formatDate } from 'date-fns';
import { DEFAULT_FORMAT } from '@Core/models/calendar/calendar';
import ProductTemplateFormField from '@Products/components/Forms/Fields/ProductTemplateFormField';
import DatePicker from '@Core/components/Inputs/DatePicker/DatePicker';
import FormValidatorField from '@Core/components/Form/Field/FormValidatorField';
import TextFieldSuffix from '@Core/components/Inputs/TextFieldSuffix';

export default {
    name: 'ProductTemplateFormDateField',
    components: {
        ProductTemplateFormField,
        DatePicker,
        FormValidatorField,
        TextFieldSuffix,
    },
    props: {
        size: {
            type: Object,
            default: () => ({}),
        },
        position: {
            type: Object,
            default: () => ({}),
        },
        parameters: {
            type: Object,
            default: () => ({}),
        },
        properties: {
            type: Object,
            default: () => ({}),
        },
        disabled: {
            type: Boolean,
            default: false,
        },
        label: {
            type: String,
            default: '',
        },
        languageCode: {
            type: String,
            required: true,
        },
    },
    computed: {
        ...mapState('product', {
            draft: state => state.draft,
        }),
        fieldData() {
            const { attribute_code } = this.properties;
            const value = this.draft[this.languageCode][attribute_code];

            return value ? new Date(value) : null;
        },
        parameter() {
            if (!this.properties.parameters) return DEFAULT_FORMAT;

            const [key] = Object.keys(this.properties.parameters);

            return this.properties.parameters[key];
        },
        fieldKey() {
            return `${this.properties.attribute_code}/${this.languageCode}`;
        },
    },
    methods: {
        ...mapActions('product', [
            'setDraftValue',
        ]),
        onFocus(isFocused) {
            if (!isFocused) {
                this.$emit('input', {
                    fieldKey: this.fieldKey,
                    languageCode: this.languageCode,
                    productId: this.$route.params.id,
                    elementId: this.properties.attribute_id,
                    value: this.fieldData
                        ? formatDate(this.fieldData, DEFAULT_FORMAT)
                        : '',
                });
            }
        },
        onValueChange(value) {
            const date = value ? formatDate(value, DEFAULT_FORMAT) : null;

            this.setDraftValue({
                languageCode: this.languageCode,
                key: this.properties.attribute_code,
                value: date,
            });
        },
    },
};
</script>
