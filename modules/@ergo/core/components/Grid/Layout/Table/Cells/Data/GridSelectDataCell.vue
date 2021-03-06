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
            <GridSelectEditCell
                v-if="isEditing"
                :value="cellData.value"
                :language-code="column.language"
                :options="options"
                :width="$el.offsetWidth"
                :height="$el.offsetHeight"
                @input="onValueChange" />
            <GridSelectPresentationCell
                v-else-if="!isEditing && cellData.value"
                :value="cellData.value"
                :suffix="data.suffix"
                :options="options"
                :is-locked="isLocked" />
        </template>
    </GridTableCell>
</template>

<script>
import { mapState } from 'vuex';
import { cellDataCompose } from '@Core/models/mappers/gridDataMapper';
import GridSelectPresentationCell from '@Core/components/Grid/Layout/Table/Cells/Presentation/GridSelectPresentationCell';
import gridDataCellMixin from '@Core/mixins/grid/cell/gridDataCellMixin';

export default {
    name: 'GridSelectDataCell',
    components: {
        GridSelectPresentationCell,
        GridSelectEditCell: () => import('@Core/components/Grid/Layout/Table/Cells/Edit/GridSelectEditCell'),
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
        options() {
            if (this.column.filter && this.column.filter.options) {
                // TODO: BE has to unify types!
                if (Array.isArray(this.column.filter.options)) return {};

                return this.column.filter.options;
            }

            return {};
        },
    },
};
</script>
