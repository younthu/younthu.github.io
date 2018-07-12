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
                        <input type="file" class="form-control" id="fi" />
                         <script>
                            function handleFile() {
                               var preview = document.getElementById('prv')
                                var file = document.getElementById('fi').files[0];
                                var reader = new FileReader();
                                var div = $("#content");
                                reader.onload = function(event) {
                                    console.log(event.target.result);
                                    $("#content").innerText=event.target.result;            
                                }
                                reader.readAsDataURL(file)
                            }
                            </script>
                            <button type="button" onclick="handleFile(); return false;" class='btn btn-primary'>click</button>
                            <div id="content"></div>
                           
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
