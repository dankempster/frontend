/*
 * Copyright © Bold Brand Commerce Sp. z o.o. All rights reserved.
 * See LICENSE for license details.
 */
<template>
    <ModalForm
        title="Upload import file"
        @close="onClose">
        <template #body>
            <Form>
                <template #body>
                    <FormSection>
                        <FormParagraph :text="paragraphText" />
                        <UploadCSVFile
                            label="Upload file"
                            required
                            :source-id="$route.params.id"
                            @progress="onUploadingCSVFile" />
                    </FormSection>
                </template>
            </Form>
        </template>
        <template #footer>
            <div>
                <Button
                    title="IMPORT NOW"
                    :disabled="isCSVUploading"
                    @click.native="onClose" />
            </div>
        </template>
    </ModalForm>
</template>

<script>
import { mapActions } from 'vuex';
import { THEME } from '@Core/defaults/theme';
import ModalForm from '@Core/components/Modal/ModalForm';
import Button from '@Core/components/Buttons/Button';
import FormParagraph from '@Core/components/Form/FormParagraph';
import Form from '@Core/components/Form/Form';
import FormSection from '@Core/components/Form/Section/FormSection';
import UploadCSVFile from '@Core/components/Inputs/UploadFile/UploadCSVFile';

export default {
    name: 'UploadImportFileModalForm',
    components: {
        Form,
        FormSection,
        FormParagraph,
        ModalForm,
        UploadCSVFile,
        Button,
    },
    data() {
        return {
            isCSVUploading: false,
        };
    },
    computed: {
        secondaryTheme() {
            return THEME.SECONDARY;
        },
        paragraphText() {
            return 'It may take some time to upload, please be patient. Maybe this is a great time for coffee?';
        },
    },
    methods: {
        ...mapActions('import', [
            'clearStorage',
        ]),
        onClose() {
            this.clearStorage();
            this.$emit('close');
        },
        onUploadingCSVFile(progress) {
            this.isCSVUploading = progress;
        },
    },
};
</script>
