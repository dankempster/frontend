/*
 * Copyright © Bold Brand Commerce Sp. z o.o. All rights reserved.
 * See LICENSE for license details.
 */
<template>
    <div class="page">
        <slot />
    </div>
</template>

<script>
export default {
    name: 'Page',
};
</script>

<style lang="scss" scoped>
    .page {
        position: relative;
        display: flex;
        flex-direction: column;
        height: 100%;
        background-color: $WHITESMOKE;
        overflow: hidden;
    }
</style>
