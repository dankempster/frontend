/*
 * Copyright © Bold Brand Commerce Sp. z o.o. All rights reserved.
 * See LICENSE for license details.
 */
<template>
    <FormSection :title="sectionTitle">
        <slot />
        <Button
            ref="addField"
            :title="addListTitle"
            :size="smallSize"
            :theme="secondaryTheme"
            :disabled="disabled"
            @click.native="onAddListElement">
            <template #prepend="{ color }">
                <IconAdd :fill-color="color" />
            </template>
        </Button>
    </FormSection>
</template>

<script>
import { SIZE, THEME } from '@Core/defaults/theme';
import IconAdd from '@Core/components/Icons/Actions/IconAdd';
import Button from '@Core/components/Buttons/Button';
import FormSection from '@Core/components/Form/Section/FormSection';

export default {
    name: 'FormListSection',
    components: {
        IconAdd,
        Button,
        FormSection,
    },
    props: {
        sectionTitle: {
            type: String,
            default: '',
        },
        addListTitle: {
            type: String,
            default: '',
        },
        disabled: {
            type: Boolean,
            default: false,
        },
    },
    computed: {
        smallSize() {
            return SIZE.SMALL;
        },
        secondaryTheme() {
            return THEME.SECONDARY;
        },
    },
    methods: {
        onAddListElement() {
            this.$refs.addField.$el.scrollIntoView(true);
            this.$emit('add');
        },
    },
};
</script>
