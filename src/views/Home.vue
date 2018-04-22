<template>
    <my-page title="JSON 编辑器" :page="page">
        <div class="btns">
            <!-- <ui-raised-button label="压缩" @click="compress" /> -->
            <ui-raised-button class="btn" label="打开 JSON 文件">
                <input type="file" class="ui-file-button" @change="fileChange($event)">
            </ui-raised-button>
            <ui-raised-button class="btn" label="清空" @click="clear" />
            <ui-raised-button class="btn" label="转 XML" @click="xml" />
            <ui-raised-button class="btn" label="上传文件" @click="asd" />
            <ui-raised-button class="btn" label="下载文件" @click="dl" />
        </div>
        <div id="jsoneditor"></div>
        <pre>{{ xmlText }}</pre>
        <ui-drawer class="settings-box" right :open="open" :docked="false" @close="toggle()">
            <ui-appbar title="设置">
                <ui-icon-button icon="close" slot="left" @click="toggle" />
            </ui-appbar>
            <div class="settings-box-body">
                <ui-text-field v-model.number="indent" label="缩进" />
            </div>
        </ui-drawer>
    </my-page>
</template>

<script>
    const Intent = window.Intent
    const JSONEditor = window.JSONEditor
    const ace = window.ace
    const $ = window.$
    const saveAs = window.saveAs

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
                            icon: 'file_download',
                            click: this.download,
                            title: '下载'
                        },
                        {
                            type: 'icon',
                            icon: 'settings',
                            click: this.toggle,
                            title: '设置'
                        }
                    ]
                }
            }
        },
        mounted() {
            // create the editor
            var container = document.getElementById('jsoneditor')
            var options = {
                modes: ['text', 'code', 'tree', 'form', 'view'],
                mode: 'code',
                ace: ace,
                indentation: this.indent
            }
            var json = {
                'array': [1, 2, 3],
                'boolean': true,
                'null': null,
                'number': 123,
                'object': {'a': 'b', 'c': 'd'},
                'string': 'Hello World'
            }
            var editor = new JSONEditor(container, options, json)
            this.editor = editor
        },
        methods: {
            init() {
                this.initWebIntents()
                // $('.zip').click(function(){
                //     if (zip_flag) {
                //         $('#json-src').keyup();
                //     }else{
                //         $('#json-target').html(current_json_str);
                //         zip_flag = true;
                //         $(this).attr('style','color:#15b374;');
                //     }

                // });
            },
            initWebIntents() {
                if (!window.intent) {
                    return
                }
                console.log(window.intent.data)
                let data = window.intent.data
                this.editor.setText(data)
                if (window.indent.action.action.includes('edit')) {
                    this.page.menu.push({
                        type: 'icon',
                        icon: 'check',
                        click: this.finish,
                        title: '完成'
                    })
                }
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
                let json = JSON.stringify(this.editor.get(), null, 4)
                var blob = new Blob([json], {type: 'text/plain;charset=utf-8'})
                saveAs(blob, 'json_yunser_com.json')
            },
            compress() {
            },
            clear() {
                this.editor.setText('')
            },
            xml() {
                let json = JSON.stringify(this.editor.get())
                console.log(json)
                var result = $.json2xml(this.editor.get())
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
            },
            finish() {
                window.intent.postResult(this.editor.getText())
                setTimeout(() => {
                    let owner = window.opener || window.parent
                    owner.window.close()
                }, 100)
            },
            asd() {
                let intent = new Intent({
                    action: 'http://webintent.yunser.com/pick',
                    type: 'text/*'
                })
                navigator.startActivity(intent, data => {
                    console.log('成功了')
                    this.editor.setText(data)
                }, data => {
                    console.log('失败')
                })
            },
            dl() {
                let intent = new Intent({
                    action: 'http://webintent.yunser.com/download',
                    type: 'text/*',
                    data: this.editor.getText(),
                    extras: {
                        fileName: '下载.json'
                    }
                })
                navigator.startActivity(intent, data => {
                    console.log('成功了')
                    this.editor.setText(data)
                }, data => {
                    console.log('失败')
                })
            }
        },
        watch: {
            indent() {
                if (this.editor) {
                    this.editor.destroy()
                }
                var container = document.getElementById('jsoneditor')
                var options = {
                    modes: ['text', 'code', 'tree', 'form', 'view'],
                    mode: 'code',
                    ace: ace,
                    indentation: this.indent
                }
                var json = {
                    'array': [1, 2, 3],
                    'boolean': true,
                    'null': null,
                    'number': 123,
                    'object': {'a': 'b', 'c': 'd'},
                    'string': 'Hello World'
                }
                var editor = new JSONEditor(container, options, json)
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