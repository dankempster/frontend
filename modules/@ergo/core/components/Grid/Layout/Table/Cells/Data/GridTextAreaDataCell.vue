/*
 * Copyright © Bold Brand Commerce Sp. z o.o. All rights reserved.
 * See LICENSE for license details.
 */
<template>
    <GridTableCell
        :row="rowIndex"
        :column="columnIndex"
        :locked="isLocked"
        :draft="cellData.isDraft"
        :error="Boolean(errorMessages)"
        :edit-key-code="editKeyCode"
        :disabled="isDisabled"
        :copyable="isCopyable"
        :selected="isSelected"
        @copy="onCopyValues">
        <template #default="{ isEditing }">
            <GridTextAreaEditCell
                v-if="isEditing"
                :value="cellData.value"
                :rte="column.parameters && column.parameters.rich_edit"
                :width="$el.offsetWidth"
                @input="onValueChange" />
            <GridPresentationCell
                v-else-if="!isEditing && cellData.value"
                :value="cellData.value"
                :suffix="data.suffix" />
        </template>
    </GridTableCell>
</template>
<script>
import { mapState } from 'vuex';
import { cellDataCompose } from '@Core/models/mappers/gridDataMapper';
import GridPresentationCell from '@Core/components/Grid/Layout/Table/Cells/Presentation/GridPresentationCell';
import gridDataCellMixin from '@Core/mixins/grid/cell/gridDataCellMixin';

export default {
    name: 'GridTextDataCell',
    components: {
        GridPresentationCell,
        GridTextAreaEditCell: () => import('@Core/components/Grid/Layout/Table/Cells/Edit/GridTextAreaEditCell'),
    },
    mixins: [gridDataCellMixin],
    computed: {
        ...mapState('grid', {
            drafts: state => state.drafts,
        }),
        cellData() {
            const check = (data, draftValue) => data !== draftValue;
            const getMappedValue = cellDataCompose(check);

            return getMappedValue(this.data.value, this.drafts[this.rowId], this.column.id);
        },
    },
};
</script>
