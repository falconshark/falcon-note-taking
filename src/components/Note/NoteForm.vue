<template>
    <div class="note-form">
        <div class="action-buttons">
            <button class="btn btn-primary" @click="save">Save</button>
        </div>
        <div class="note-info-wrapper">
            <input id="note-title" class="input-text" v-model="noteTitle" placeholder="New Note" type="text" />
            <div class="notebook-select">
                <i class="bi bi-book"></i>
                <el-select v-model="notebook" filterable placeholder="请选择">
                    <el-option v-for="notebook in notebooks" :key="notebook.path" :label="notebook.name" :value="notebook.path"></el-option>
                </el-select>
            </div>
        </div>
        <div class="note-editor">
            <QuillEditor ref="editor" theme="snow" />
        </div>
    </div>
</template>
    
<script>
import { mapState } from 'pinia';
import { useStorageStore } from '@/stores/storage';
import { QuillEditor } from '@vueup/vue-quill';
import Common from '@/lib/Common';
import Storage from '@/lib/Storage';
import Note from '@/lib/Note';
import '@vueup/vue-quill/dist/vue-quill.snow.css';

export default {
    name: 'CreateNote',
    components: {
        QuillEditor,
    },
    props: {
        action: String,
        note: {
            type: Object,
            required: false,
        },
    },
    mounted(){
        this.loadNotebooks();
    },
    data() {
        return {
            noteTitle: '',
            notebook: 'Uncategorized',
            notebooks: [{'name': 'Uncategorized', 'path': ''}],
        };
    },
    methods: {
        async loadNotebooks(){
            const rootFolderContent = await Storage.listDropboxFiles(this.dbx, '');
            const folders = await Storage.filterDropboxFolders(rootFolderContent);
            const notebooks = [];
            for(let i = 0; i < folders.length; i++){
                let notebook = folders[i];
                notebook = {
                    name: notebook.name,
                    path: notebook.path_lower,
                };
                notebooks.push(notebook);
            }
            this.notebooks = this.notebooks.concat(notebooks);
        },
        async save() {
            const title = this.noteTitle;
            const notebook = this.notebook;
            const content = this.$refs.editor.getHTML();
            switch (this.action) {
                case 'create':
                    const currentDate = Common.genDate();
                    const noteHtml = Note.createNoteHtml(title, content);
                    //By default, save the file to target notebook (folder)
                    let fileName = `${notebook}/${title}-${currentDate}.html`;
                    //But if target notebook is 'Uncategorized', save it to root folder
                    if(notebook === 'Uncategorized'){
                        fileName = `/${title}-${currentDate}.html`;
                    }
                    try{
                        await Storage.createDropboxFile(this.dbx, fileName, noteHtml);
                        this.$router.push({'path': '/notes'});
                    }catch(ex){
                        console.error(ex);
                    }
                    break;
                case 'update':
                    break;
                default:
                    break;
            }
        },
    },
    computed: {
        ...mapState(useStorageStore, ['dbx'])
    },
}
</script>
    
<style lang="scss" scoped>
.note-form{
    flex-basis: 70%;
    padding-left: 20px;
    padding-right: 20px;
    padding-top: 20px;
}
.action-buttons {
    text-align: right;
    width: 100%;

    .btn {
        display: inline-block;
    }
}

.note-info-wrapper {
    margin-bottom: 20px;

    .input-text {
        font-size: 1.5em;
        font-weight: bold;
        border: 0;
        width: 100%;
        border-bottom: 1px solid #ddd;
        padding-top: 10px;
        padding-bottom: 10px;
    }
}

.notebook-select {
    margin-top: 10px;
    display: flex;
    align-items: center;

    .bi {
        font-size: 1.4em;
        color: rgb(127, 127, 127);
        margin-right: 10px;
    }
}
</style>