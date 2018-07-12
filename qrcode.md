---
layout: page
type: page
title: 二维码(QRCode)编解码
---

<div class="container p-t-md">
    <div class="row">
        <div class="col-md-6">
            <ul class="list-unstyled nav nav-tabs">
                <li class="nav-item" style="list-style-type: none;">
                    <a class="nav-link active" data-toggle="tab" href="#currentPreferences">生成QR Code</a>
                </li>
                <li class="nav-item" style="list-style-type: none;">
                    <a class="nav-link" data-toggle="tab" href="#alternative"> 解码QRCode </a>
                </li>
            </ul>
            <div class="tab-content">
                <div role="tabpanel" class="tab-pane active" id="currentPreferences">
                    <ul class="list-group media-list media-list-stream">
                        <div>
                            <div class="input-group mb-3" title="Popover title"
                                 data-container="body" data-toggle="popover" data-placement="bottom"
                                 data-content="底部的 Popover 中的一些内容">
                                <input id="text" type="text" class="form-control" placeholder="" aria-label="Recipient"
                                       aria-describedby="basic-addon2">
                                <div class="input-group-append">
                                    <span class="input-group-text" id="basic-addon2">回车自动刷新</span>
                                </div>
                            </div>
                            <span class="badge badge-secondary" style="display:block" id="tips">请右键点击二维码 -> 保存图片</span>
                            <div id="qrcode" style="width:300px; height:300px; margin-top:15px;"></div>
                        </div>
                    </ul>
                </div>
                <div role="tabpanel" class="tab-pane fade in" id="alternative">
                    <ul class="list-group media-list media-list-stream">
                        <div>
                        
                        <script type="text/javascript" src="{{ site.assets }}/js/jsqrcode/grid.js"></script>
                        <script type="text/javascript" src="{{ site.assets }}/js/jsqrcode/version.js"></script>
                        <script type="text/javascript" src="{{ site.assets }}/js/jsqrcode/detector.js"></script>
                        <script type="text/javascript" src="{{ site.assets }}/js/jsqrcode/formatinf.js"></script>
                        <script type="text/javascript" src="{{ site.assets }}/js/jsqrcode/errorlevel.js"></script>
                        <script type="text/javascript" src="{{ site.assets }}/js/jsqrcode/bitmat.js"></script>
                        <script type="text/javascript" src="{{ site.assets }}/js/jsqrcode/datablock.js"></script>
                        <script type="text/javascript" src="{{ site.assets }}/js/jsqrcode/bmparser.js"></script>
                        <script type="text/javascript" src="{{ site.assets }}/js/jsqrcode/datamask.js"></script>
                        <script type="text/javascript" src="{{ site.assets }}/js/jsqrcode/rsdecoder.js"></script>
                        <script type="text/javascript" src="{{ site.assets }}/js/jsqrcode/gf256poly.js"></script>
                        <script type="text/javascript" src="{{ site.assets }}/js/jsqrcode/gf256.js"></script>
                        <script type="text/javascript" src="{{ site.assets }}/js/jsqrcode/decoder.js"></script>
                        <script type="text/javascript" src="{{ site.assets }}/js/jsqrcode/qrcode.js"></script>
                        <script type="text/javascript" src="{{ site.assets }}/js/jsqrcode/findpat.js"></script>
                        <script type="text/javascript" src="{{ site.assets }}/js/jsqrcode/alignpat.js"></script>
                        <script type="text/javascript" src="{{ site.assets }}/js/jsqrcode/databr.js"></script>
                         <script>
                            function handleFile() {
                               var preview = document.getElementById('prv')
                                var file = document.getElementById('fi').files[0];
                                var reader = new FileReader();
                                var div = $("#content");
                                reader.onload = function(event) {
                                    console.log(event.target.result);
                                    // $("#content").innerText=event.target.result; 
                                    setTimeout(function(){
                                        var image = new Image();
                                        image.src = event.target.result;
                                        image.style="height:300px; width=300px"
                                        $("#image_preview").append(image) 
                                    }, 200);

                                    qrcode.callback = function(data){
                                        console.log(data)
                                        $("#decoded").text( data);
                                    };

                                    qrcode.decode(event.target.result)          
                                }
                                reader.readAsDataURL(file)

                            };
                            
                            $("#fi").on("change",function(){
                                handleFile();
                            });
                        </script>


                            <input type="file" class="form-control" id="fi" />
                            <div id='image_preview' style="padding:20" />

                            <button type="button" onclick="handleFile(); return false;" class='btn btn-primary' style="margin-top:20px;margin-bottom:20px">解码</button>
                            
                            <div  class='card card-block bg-faded'><span id="decoded"></span></div>
                            
                            
                        </div>
                    </ul>
                </div>
                
            </div>
        </div>
    </div>
</div>


<script type="text/javascript">

    $(document).ready(function () {
                var qrcode = null;

                function makeCode() {
                    let elText = $("#text").val();

                    if (!elText) {
                        // alert("Input a text");
                        // $("[data-toggle='popover']").popover();
                        $("#text").focus();
                        $("#tips").hide()
                        return;
                    }

                    $("#tips").show()
                    $('#qrcode').empty()

                    setTimeout(function () { // if no delay, there will be empty
                                qrcode = $("#qrcode").qrcode({width: 64, height: 64, text: elText})
                            }
                            , 500);
                }

                makeCode();

                $("#text").on("blur", function () {
                    makeCode();
                }).on("keydown", function (e) {
                    if (e.keyCode == 13) {
                        makeCode();
                    }
                });
            }
    )
</script>
