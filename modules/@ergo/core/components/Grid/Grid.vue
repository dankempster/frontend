/*
 * Copyright © Bold Brand Commerce Sp. z o.o. All rights reserved.
 * See LICENSE for license details.
 */

<template>
    <div
        data-cy="grid"
        class="grid">
        <GridHeader
            v-if="isHeaderVisible"
            :row-height="tableLayoutConfig.rowHeight"
            :layout="layout"
            :table-layout-config="tableLayoutConfig"
            :collection-layout-config="collectionLayoutConfig"
            :is-advanced-filters="isAdvancedFilters"
            :is-collection-layout="isCollectionLayout"
            :is-centered-view="isCenteredView"
            :filters="advancedFilters"
            @layoutChange="onLayoutChange"
            @applySettings="onApplySettings"
            @dropFilter="onDropFilter"
            @updateFilter="onUpdateFilterAtIndex"
            @removeFilter="onRemoveFilterAtIndex"
            @removeGhostFilter="onRemoveGhostFilterAtIndex"
            @clearFilter="onClearFilterAtIndex"
            @swapFilters="onSwapFiltersPosition"
            @applyFilter="emitFetchData"
            @removeAllFilters="onRemoveAll">
            <template #actions>
                <slot name="actions" />
            </template>
            <template #configuration>
                <slot name="configuration" />
            </template>
        </GridHeader>
        <div :class="['grid__body', { 'grid__body--disabled': isColumnExists }]">
            <GridDropZone
                v-show="isListElementDragging && !isColumnExists"
                @drop="onDropColumn" />
            <GridTableLayout
                v-if="isTableLayout"
                :columns="columns"
                :data="data"
                :advanced-filters-values="advancedFiltersValues"
                :current-page="currentPage"
                :max-rows="maxRows"
                :row-height="tableLayoutConfig.rowHeight"
                :is-editable="isEditable"
                :is-select-column="isSelectColumn"
                :is-basic-filter="isBasicFilter"
                @sort="onSortColumn"
                @filter="onFilterChange"
                @editCell="onEditCell"
                @editCells="onEditCells"
                @focusCell="onFocusCell"
                @editRow="onEditRow"
                @removeRow="onRemoveRow"
                @removeColumn="onRemoveColumn"
                @swapColumns="onSwapColumns"
                @insertColumn="onInsertColumn" />
            <GridCollectionLayout
                v-else-if="isCollectionLayout && collectionData.length"
                :data="collectionData"
                :columns-number="collectionLayoutConfig.columnsNumber"
                :object-fit="collectionLayoutConfig.scaling"
                @editRow="onEditRow" />
            <GridPlaceholder v-if="dataCount === 0" />
        </div>
        <div
            class="grid__footer"
            v-if="isFooterVisible">
            <GridPageSelector
                :value="maxRows"
                :max-rows="dataCount"
                @input="setMaxRows" />
            <GridPagination
                :value="currentPage"
                :max-page="maxPage"
                @input="setCurrentPage" />
            <slot name="appendFooter" />
        </div>
    </div>
</template>

<script>
import { mapState } from 'vuex';
import {
    ROW_HEIGHT,
    GRID_LAYOUT,
    DATA_LIMIT,
    IMAGE_SCALING,
    COLUMNS_NUMBER,
    COLUMN_ACTIONS_ID,
} from '@Core/defaults/grid';
import {
    insertCookieAtIndex,
} from '@Core/models/cookies';

export default {
    name: 'Grid',
    components: {
        GridHeader: () => import('@Core/components/Grid/Header/GridHeader'),
        GridTableLayout: () => import('@Core/components/Grid/Layout/Table/GridTableLayout'),
        GridCollectionLayout: () => import('@Core/components/Grid/Layout/Collection/GridCollectionLayout'),
        GridPlaceholder: () => import('@Core/components/Grid/GridPlaceholder'),
        GridPagination: () => import('@Core/components/Grid/Footer/GridPagination'),
        GridPageSelector: () => import('@Core/components/Grid/Footer/GridPageSelector'),
        GridDropZone: () => import('@Core/components/Grid/GridDropZone'),
    },
    props: {
        columns: {
            type: Array,
            default: () => [],
        },
        data: {
            type: Object,
            default: () => ({}),
        },
        advancedFilters: {
            type: Array,
            default: () => [],
        },
        collectionCellBinding: {
            type: Object,
            default: () => ({
                imageColumn: '',
                descriptionColumn: '',
            }),
        },
        defaultLayout: {
            type: String,
            default: GRID_LAYOUT.TABLE,
            validator: value => Object.values(GRID_LAYOUT).indexOf(value) !== -1,
        },
        dataCount: {
            type: Number,
            required: true,
        },
        isAdvancedFilters: {
            type: Boolean,
            default: false,
        },
        isCollectionLayout: {
            type: Boolean,
            default: false,
        },
        isCenteredView: {
            type: Boolean,
            default: false,
        },
        isEditable: {
            type: Boolean,
            default: true,
        },
        isBasicFilter: {
            type: Boolean,
            default: false,
        },
        isSelectColumn: {
            type: Boolean,
            default: false,
        },
        isFooterVisible: {
            type: Boolean,
            default: true,
        },
        isHeaderVisible: {
            type: Boolean,
            default: false,
        },
    },
    data() {
        return {
            layout: this.defaultLayout,
            maxRows: DATA_LIMIT,
            currentPage: 1,
            filters: {},
            sortedColumn: {},
            collectionLayoutConfig: {
                columnsNumber: COLUMNS_NUMBER.FOURTH_COLUMNS.value,
                scaling: IMAGE_SCALING.FIT_TO_SIZE.value,
            },
            tableLayoutConfig: {
                rowHeight: ROW_HEIGHT.MEDIUM,
            },
        };
    },
    computed: {
        ...mapState('draggable', {
            isListElementDragging: state => state.isListElementDragging,
            draggedElement: state => state.draggedElement,
        }),
        isColumnExists() {
            const draggedElIndex = this.columns.findIndex(
                column => column.id === this.draggedElement,
            );

            return draggedElIndex !== -1;
        },
        isTableLayout() {
            return this.layout === GRID_LAYOUT.TABLE;
        },
        maxPage() {
            return Math.ceil(this.dataCount / this.maxRows) || 1;
        },
        advancedFiltersValues() {
            const { length } = this.advancedFilters;
            const advancedFiltersValues = {};

            for (let i = 0; i < length; i += 1) {
                if (Object.keys(this.advancedFilters[i].value).length > 1) {
                    advancedFiltersValues[this.advancedFilters[i].id] = true;
                }
            }

            return advancedFiltersValues;
        },
        collectionData() {
            const { imageColumn, descriptionColumn } = this.collectionCellBinding;

            if (!(imageColumn && descriptionColumn) || !this.data[COLUMN_ACTIONS_ID]) {
                return [];
            }

            const collectionData = [];
            const actionKeys = Object.keys(this.data[COLUMN_ACTIONS_ID]);

            for (let i = 0; i < this.data[descriptionColumn].length; i += 1) {
                const actions = {};

                if (this.data[COLUMN_ACTIONS_ID]) {
                    for (let j = 0; j < actionKeys.length; j += 1) {
                        const actionKey = actionKeys[j];

                        actions[actionKey] = this.data[COLUMN_ACTIONS_ID][actionKey][i];
                    }
                }

                collectionData.push({
                    actions,
                    image: this.data[imageColumn]
                        ? this.data[imageColumn][i].value
                        : '',
                    description: this.data[descriptionColumn]
                        ? this.data[descriptionColumn][i].value
                        : '',
                });
            }

            return collectionData;
        },
    },
    methods: {
        onApplySettings({ tableConfig, collectionConfig }) {
            this.tableLayoutConfig = tableConfig;
            this.collectionLayoutConfig = collectionConfig;
        },
        onLayoutChange(layout) {
            this.layout = layout;
        },
        onInsertColumn(payload) {
            this.$emit('insertColumn', payload);
        },
        onEditCell(payload) {
            this.$emit('editCell', payload);
        },
        onEditCells(payload) {
            this.$emit('editCells', payload);
        },
        onFocusCell(payload) {
            this.$emit('focusCell', payload);
        },
        onEditRow(args) {
            this.$emit('editRow', args);
        },
        onRemoveRow(index) {
            this.$emit('removeRow', index);
        },
        onDropColumn(columnId) {
            insertCookieAtIndex({
                cookies: this.$cookies,
                cookieName: `GRID_CONFIG:${this.$route.name}`,
                index: this.isSelectColumn ? 1 : 0,
                data: columnId,
            });
            this.$emit('dropColumn', columnId);
        },
        onSwapColumns(payload) {
            this.$emit('swapColumns', payload);
        },
        onRemoveColumn(index) {
            this.$emit('removeColumn', index);
        },
        onSortColumn(sortedColumn) {
            this.sortedColumn = sortedColumn;
            this.emitFetchData();
        },
        onFilterChange(filters) {
            this.filters = filters;
            this.emitFetchData();
        },
        setCurrentPage(page) {
            this.currentPage = page;
            this.emitFetchData();
        },
        setMaxRows(maxRows) {
            const number = Math.trunc(maxRows);

            if (number !== this.maxRows) {
                this.currentPage = 1;
                this.maxRows = number;

                this.emitFetchData();
            }
        },
        emitFetchData() {
            this.$emit('fetchData', {
                sortedColumn: this.sortedColumn,
                filters: this.filters,
                offset: (this.currentPage - 1) * this.maxRows,
                limit: this.maxRows,
            });
        },
        onUpdateFilterAtIndex(payload) {
            this.$emit('updateFilter', payload);
            this.emitFetchData();
        },
        onRemoveFilterAtIndex(index) {
            this.$emit('removeFilter', index);
            this.emitFetchData();
        },
        onRemoveGhostFilterAtIndex(index) {
            this.$emit('removeFilter', index);
        },
        onRemoveAll() {
            this.$emit('removeAllFilters');
            this.emitFetchData();
        },
        onDropFilter(payload) {
            this.$emit('dropFilter', payload);
        },
        onClearFilterAtIndex(index) {
            this.$emit('clearFilter', index);
        },
        onSwapFiltersPosition(payload) {
            this.$emit('swapFilters', payload);
        },
    },
};
</script>

<style lang="scss" scoped>
    .grid {
        $grid: &;

        position: relative;
        display: flex;
        flex: 1 1 auto;
        flex-direction: column;
        min-width: 0;
        overflow: hidden;

        &__body {
            position: relative;
            display: flex;
            flex: 1 1 auto;
            flex-direction: column;
            justify-content: space-between;
            height: 0;
            border: $BORDER_1_GREY;
            background-color: $WHITESMOKE;

            &--disabled {
                &::after {
                    position: absolute;
                    z-index: $Z_INDEX_NEGATIVE;
                    width: 100%;
                    height: 100%;
                    content: "";
                }

                &--disabled {
                    &::after {
                        z-index: $Z_INDEX_LVL_4;
                    }
                }
            }
        }

        &__footer {
            z-index: $Z_INDEX_LVL_5;
            display: flex;
            justify-content: space-between;
            align-items: center;
            height: 56px;
            border: $BORDER_1_GREY;
            border-top: unset;
            padding: 12px 16px;
            box-sizing: border-box;
            background-color: $WHITE;
            box-shadow: $ELEVATOR_6_DP;
        }
    }
</style>
