<template>
    <my-page title="JSON 编辑器" :page="page">
        <div class="btns">
            <!-- <ui-raised-button label="压缩" @click="compress" /> -->
            <ui-raised-button class="btn" label="打开 JSON 文件">
                <input type="file" class="ui-file-button" @change="fileChange($event)">
            </ui-raised-button>
            <ui-raised-button class="btn" label="下载" @click="download" />
            <ui-raised-button class="btn" label="清空" @click="clear" />
            <ui-raised-button class="btn" label="转 XML" @click="xml" />
        </div>
        <div id="jsoneditor"></div>
        <pre>{{ xmlText }}</pre>
        <ui-drawer class="settings-box" right :open="open" :docked="false" @close="toggle()">
            <ui-appbar title="设置"></ui-appbar>
            <div class="settings-box-body">
                <ui-text-field v-model.number="indent" label="缩进" />
            </div>
        </ui-drawer>
    </my-page>
</template>

<script>
    /* eslint-disable */
    export default {
        data () {
            return {
                xmlText: '',
                open: false,
                indent: 4,
                page: {
                    menu: [
                        {
                            type: 'icon',
                            icon: 'settings',
                            click: this.toggle
                        }
                    ]
                }
            }
        },
        mounted() {
             // create the editor
            var container = document.getElementById('jsoneditor');
            var options = {
                modes: ['text', 'code', 'tree', 'form', 'view'],
                mode: 'code',
                ace: ace,
                indentation: this.indent
            };
            var json = {
                'array': [1, 2, 3],
                'boolean': true,
                'null': null,
                'number': 123,
                'object': {'a': 'b', 'c': 'd'},
                'string': 'Hello World'
            };
            var editor = new JSONEditor(container, options, json);
            this.editor = editor
        },
        methods: {
            init() {
                $('.zip').click(function(){
                    if (zip_flag) {
                        $('#json-src').keyup();
                    }else{
                        $('#json-target').html(current_json_str);
                        zip_flag = true;
                        $(this).attr('style','color:#15b374;');
                    }

                });
            },
            showOptions() {

            },
            fileChange(e) {
                let _this = this
                let file = e.target.files[0]

                _this.result = {}

                let reader = new FileReader()
                reader.onload = e => {
                    let content = e.target.result
                    this.editor.set(JSON.parse(content))
                }
                reader.readAsText(file, 'utf-8')
            },
            download() {
                let json = JSON.stringify(this.editor.get())
                var blob = new Blob([json], {type: "text/plain;charset=utf-8"})
                saveAs(blob, "json_yunser_com.json")
            },
            compress() {
            },
            clear() {
                this.editor.set({})
            },
            xml() {
                let json = JSON.stringify(this.editor.get())
                console.log(json)
                var result = $.json2xml(this.editor.get());
                console.log(result)
                this.xmlText = result
                // $('#json-target').text(result);
                    // if (xml_flag) {
                    //     $('#json-src').keyup();
                    // }else{
                        
                    //     $('#json-target').html('<textarea style="width:100%;height:100%;border:0;resize:none;">'+result+'</textarea>');
                    //     xml_flag = true;
                    //     $(this).attr('style','color:#15b374;');
                    // }
            },
            toggle () {
                this.open = !this.open
            }
        },
        watch: {
            indent() {
                if (this.editor) {
                    this.editor.destroy()
                }
                var container = document.getElementById('jsoneditor');
                var options = {
                    modes: ['text', 'code', 'tree', 'form', 'view'],
                    mode: 'code',
                    ace: ace,
                    indentation: this.indent
                };
                var json = {
                    'array': [1, 2, 3],
                    'boolean': true,
                    'null': null,
                    'number': 123,
                    'object': {'a': 'b', 'c': 'd'},
                    'string': 'Hello World'
                };
                var editor = new JSONEditor(container, options, json);
                this.editor = editor
            }
        }
    }
</script>

<style lang="scss" scoped>
    .btns {
        margin-bottom: 16px;
        .btn {
            margin-right: 8px;
        }
    }
    .settings-box {
        width: 256px;
        .settings-box-body {
            padding: 16px;
        }
    }
</style>

<style lang="scss">
    .jsoneditor {
        height: 400px !important;
    }
</style>