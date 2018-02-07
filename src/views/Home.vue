<template>
    <my-page title="JSON">
        <ui-row gutter>
            <ui-col width="100" tablet="100" desktop="50">
                <div class="numberedtextarea-wrapper ">
					<textarea id="json-src" class="form-control" placeholder="在此输入json字符串或XML字符串...">{
    "Json解析":"支持格式化高亮折叠",
    "支持XML转换":"支持XML转换Json,Json转XML",
    "Json格式验证":"更详细准确的错误信息"
}</textarea>
                    <div class="numberedtextarea-line-numbers" style="padding-top: 0px; line-height: 17.1429px; font-family: Tahoma, Verdana, Menlo, Monaco, Consolas, &#39;Helvetica Neue&#39;, Helvetica, &#39;Courier New&#39;, 微软雅黑, monospace, Arial, sans-serif, 幼圆, 黑体; width: 30px;"><div class="numberedtextarea-number numberedtextarea-number-1">1</div><div class="numberedtextarea-number numberedtextarea-number-2">2</div><div class="numberedtextarea-number numberedtextarea-number-3">3</div><div class="numberedtextarea-number numberedtextarea-number-4">4</div><div class="numberedtextarea-number numberedtextarea-number-5" style="margin-bottom: 10px;">5</div></div></div>
            </ui-col>
            <ui-col width="100" tablet="100" desktop="50">
                <div class="tool-box">
                    <div style="padding:10px;border-bottom:solid 1px #eee;font-size:14px;" class="tool">
                        <a href="#" class="tip zip" title="" data-placement="bottom" data-original-title="压缩" style="color:#999;">压缩</a>
                        <a href="#" class="tip xml" title="" data-placement="bottom" data-original-title="转XML" style="color:#999;">转XML</a>
                        <a href="#" class="tip shown" title="" data-placement="bottom" data-original-title="显示行号">显示行号</a>
                        <a href="#" class="tip clear" title="" data-placement="bottom" data-original-title="清空">清空</a>
                    </div>
                    <div id="right-box" style="height:510px;border-bottom:solid 1px #E5EBEE;border-radius:0;resize: none;overflow-y:scroll; outline:none;position:relative;font-size:12px;font-family:Menlo,Monaco,Consolas,&#39;微软雅黑&#39;, monospace, Arial,sans-serif,&#39;黑体&#39;;">
                        <div id="line-num" style="background-color:#fafafa;padding:0px 8px;float:left;border-right:dashed 1px #E5EBEE;display:none;z-index:-1;color:#999;position:absolute;text-align:center;over-flow:hidden;"><div>1<div><div>2<div><div>3<div><div>4<div><div>5<div></div></div></div></div></div></div></div></div></div></div></div>
                        <div class="ro" id="json-target" style="padding:0px 25px;"><span data-type="object" data-inner="&lt;i style=&quot;cursor:pointer;&quot; class=&quot;fa fa-minus-square-o&quot; onclick=&quot;hide(this)&quot;&gt;&lt;/i&gt;{&lt;br&gt;&amp;nbsp;&amp;nbsp;&amp;nbsp;&amp;nbsp;&lt;span class=&quot;json_key&quot;&gt;&quot;Json解析&quot;&lt;/span&gt;:&lt;span class=&quot;json_string&quot;&gt;&quot;支持格式化高亮折叠&quot;&lt;/span&gt;,&lt;br&gt;&amp;nbsp;&amp;nbsp;&amp;nbsp;&amp;nbsp;&lt;span class=&quot;json_key&quot;&gt;&quot;支持XML转换&quot;&lt;/span&gt;:&lt;span class=&quot;json_string&quot;&gt;&quot;支持XML转换Json,Json转XML&quot;&lt;/span&gt;,&lt;br&gt;&amp;nbsp;&amp;nbsp;&amp;nbsp;&amp;nbsp;&lt;span class=&quot;json_key&quot;&gt;&quot;Json格式验证&quot;&lt;/span&gt;:&lt;span class=&quot;json_string&quot;&gt;&quot;更详细准确的错误信息&quot;&lt;/span&gt;&lt;br&gt;}"><i style="cursor:pointer;" class="fa fa-minus-square-o" onclick="hide(this)"></i>{<br>&nbsp;&nbsp;&nbsp;&nbsp;<span class="json_key">"Json解析"</span>:<span class="json_string">"支持格式化高亮折叠"</span>,<br>&nbsp;&nbsp;&nbsp;&nbsp;<span class="json_key">"支持XML转换"</span>:<span class="json_string">"支持XML转换Json,Json转XML"</span>,<br>&nbsp;&nbsp;&nbsp;&nbsp;<span class="json_key">"Json格式验证"</span>:<span class="json_string">"更详细准确的错误信息"</span><br>}</span></div>
                    </div>
                </div>
            </ui-col>
        </ui-row>
        <div id="input-box" class="row">
            <div class="col-md-5">

            </div>
            <div class="col-md-7">


            </div>
        </div>
    </my-page>
</template>

<script>
    /* eslint-disable */

    export default {
        data () {
            return {
            }
        },
        mounted() {
            this.init()
        },
        methods: {
            init() {
                $('textarea').numberedtextarea();
                var current_json = '';
                var current_json_str = '';
                var xml_flag = false;
                var zip_flag = false;
                var shown_flag = false;
//                $('.tip').tooltip();
                function init(){
                    xml_flag = false;
                    zip_flag = false;
                    shown_flag = false;
                    renderLine();
                    $('.xml').attr('style','color:#999;');
                    $('.zip').attr('style','color:#999;');

                }
                $('#json-src').keyup(function(){
                    init();
                    var content = $.trim($(this).val());
                    var result = '';
                    if (content!='') {
                        //如果是xml,那么转换为json
                        if (content.substr(0,1) === '<' && content.substr(-1,1) === '>') {
                            try{
                                var json_obj = $.xml2json(content);
                                content = JSON.stringify(json_obj);
                            }catch(e){
                                result = '解析错误：<span style="color: #f1592a;font-weight:bold;">' + e.message + '</span>';
                                current_json_str = result;
                                $('#json-target').html(result);
                                return false;
                            }

                        }
                        try{
                            current_json = jsonlint.parse(content);
                            current_json_str = JSON.stringify(current_json);
                            //current_json = JSON.parse(content);
                            result = new JSONFormat(content,4).toString();
                        }catch(e){
                            result = '<span style="color: #f1592a;font-weight:bold;">' + e + '</span>';
                            current_json_str = result;
                        }

                        $('#json-target').html(result);
                    }else{
                        $('#json-target').html('');
                    }

                });
                $('.xml').click(function(){
                    if (xml_flag) {
                        $('#json-src').keyup();
                    }else{
                        var result = $.json2xml(current_json);
                        $('#json-target').html('<textarea style="width:100%;height:100%;border:0;resize:none;">'+result+'</textarea>');
                        xml_flag = true;
                        $(this).attr('style','color:#15b374;');
                    }

                });
                $('.shown').click(function(){
                    if (!shown_flag) {
                        renderLine();
                        $('#json-src').attr("style","height:553px;padding:0 10px 10px 40px;border:0;border-right:solid 1px #ddd;border-bottom:solid 1px #ddd;border-radius:0;resize: none; outline:none;font-size:10px;");
                        $('#json-target').attr("style","padding:0px 40px;");
                        $('#line-num').show();
                        $('.numberedtextarea-line-numbers').show();
                        shown_flag = true;
                        $(this).attr('style','color:#15b374;');
                    }else{
                        $('#json-src').attr("style","height:553px;padding:0 10px 10px 20px;border:0;border-right:solid 1px #ddd;border-bottom:solid 1px #ddd;border-radius:0;resize: none; outline:none;font-size:10px;");
                        $('#json-target').attr("style","padding:0px 10px;");
                        $('#line-num').hide();
                        $('.numberedtextarea-line-numbers').hide();
                        shown_flag = false;
                        $(this).attr('style','color:#999;');
                    }
                });
                function renderLine(){
                    var line_num = $('#json-target').height()/20;
                    $('#line-num').html("");
                    var line_num_html = "";
                    for (var i = 1; i < line_num+1; i++) {
                        line_num_html += "<div>"+i+"<div>";
                    }
                    $('#line-num').html(line_num_html);
                }
                $('.zip').click(function(){
                    if (zip_flag) {
                        $('#json-src').keyup();
                    }else{
                        $('#json-target').html(current_json_str);
                        zip_flag = true;
                        $(this).attr('style','color:#15b374;');
                    }

                });
                $('.clear').click(function(){
                    $('#json-src').val('');
                    $('#json-target').html('');
                });
                $('.save').click(function(){
                    var content = JSON.stringify(current_json);
                    $('#txt-content').val(content);
                    $("#form-save").submit();
                });
                $('#json-src').keyup();
            }
        }
    }
</script>

<style scoped>
    .form-control {
        display: block;
        width: 100%;
    }
</style>
