/*
 * Copyright © Bold Brand Commerce Sp. z o.o. All rights reserved.
 * See LICENSE for license details.
 */
<template>
    <label
        class="info-hint"
        :title="hint">
        <IconError :fill-color="redColor" />
    </label>
</template>

<script>
import IconError from '@Core/components/Icons/Feedback/IconError';
import { RED } from '@Core/assets/scss/_js-variables/colors.scss';

export default {
    name: 'ErrorHint',
    components: {
        IconError,
    },
    props: {
        hint: {
            type: String,
            required: true,
        },
    },
    computed: {
        redColor() {
            return RED;
        },
    },
};
</script>

<style lang="scss" scoped>
    .info-hint {
        display: flex;
        justify-content: center;
        align-items: center;
        width: 24px;
        height: 24px;
        transform: scale(0.8);
    }
</style>
