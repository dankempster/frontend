/*
 * Copyright © Bold Brand Commerce Sp. z o.o. All rights reserved.
 * See LICENSE for license details.
 */
<template>
    <ResponsiveCenteredViewTemplate>
        <template #content>
            <Grid
                :columns="columns"
                :data="data"
                :data-count="dataCount"
                :is-editable="false"
                :is-footer-visible="false" />
        </template>
    </ResponsiveCenteredViewTemplate>
</template>

<script>
import privilegesDefaults from '@Users/defaults/privileges';
import { getMappedGridData } from '@Users/models/gridDataMapper';
import { getSortedColumnsByIDs } from '@Core/models/mappers/gridDataMapper';
import Grid from '@Core/components/Grid/Grid';
import ResponsiveCenteredViewTemplate from '@Core/components/Layout/Templates/ResponsiveCenteredViewTemplate';

export default {
    name: 'UserPrivilegesGridTab',
    components: {
        ResponsiveCenteredViewTemplate,
        Grid,
    },
    asyncData({ app, store, route }) {
        const { privileges: privilegesDictionary } = store.state.dictionaries;
        const { privileges } = store.state.authentication.user;
        const {
            data, columns,
        } = getMappedGridData({
            fullDataList: privilegesDictionary,
            selectedData: privileges,
            defaults: privilegesDefaults,
        });
        const config = app.$cookies.get(`GRID_CONFIG:${route.name}`);
        const sortedColumns = config
            ? getSortedColumnsByIDs(columns, config.split(','))
            : columns;

        return {
            dataCount: privilegesDictionary.length,
            columns: sortedColumns,
            data,
        };
    },
};
</script>
