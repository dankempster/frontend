/*
 * Copyright © Bold Brand Commerce Sp. z o.o. All rights reserved.
 * See LICENSE for license details.
 */
<template>
    <div class="menu-section">
        <MenuListHeader
            v-if="sectionTitle"
            :title="sectionTitle" />
        <ul class="menu-section__list">
            <MenuListElement
                v-for="(item, index) in sectionMenu"
                :key="index"
                :item="item" />
        </ul>
    </div>
</template>

<script>
export default {
    name: 'MenuList',
    components: {
        MenuListElement: () => import('@Core/components/MenuList/MenuListElement'),
        MenuListHeader: () => import('@Core/components/MenuList/MenuListHeader'),
    },
    props: {
        sectionTitle: {
            type: String,
            default: null,
        },
        sectionMenu: {
            type: Array,
            required: true,
        },
    },
};
</script>

<style lang="scss">
    @import "@Core/assets/scss/navigation/menu-list.scss";
</style>
