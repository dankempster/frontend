/*
 * Copyright © Bold Brand Commerce Sp. z o.o. All rights reserved.
 * See LICENSE for license details.
 */
<template>
    <DropDownFooter :small="true">
        <Button
            :theme="secondaryTheme"
            :size="tinySize"
            title="CLEAR"
            @click.native="onClear" />
    </DropDownFooter>
</template>

<script>
import { SIZE, THEME } from '@Core/defaults/theme';
import DropDownFooter from '@Core/components/Inputs/Select/DropDown/Footers/DropDownFooter';
import Button from '@Core/components/Buttons/Button';

export default {
    name: 'SelectDropdownFooter',
    components: {
        DropDownFooter,
        Button,
    },
    computed: {
        tinySize() {
            return SIZE.TINY;
        },
        secondaryTheme() {
            return THEME.SECONDARY;
        },
    },
    methods: {
        onClear() {
            this.$emit('clear');
        },
    },
};
</script>
