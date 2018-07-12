---
layout: page
title: Base64编解码工具
---

<div class="panel">

 <div>
   <form>
     <div class="form-group">
       <label for="source">源字符串:</label>
       <textarea class="form-control" rows="5" id="source"></textarea>
     </div>
   
   
        <button type="button" class="btn btn-primary glyphicon glyphicon-arrow-down" id='encode'><i class="fa fa-arrow-down" aria-hidden="true"></i> Base64编码</button>

        <button type="button" class="btn btn-success glyphicon glyphicon-arrow-up" id='decode'> <i class="fa fa-arrow-up" aria-hidden="true"></i> Base64解码</button>
        
        <div class="form-group">
           <label for="encoded">Base64编码字符串:</label>
           <textarea class="form-control" rows="5" id="encoded"></textarea>
         </div>
   </form>
 </div>
<div class="panel">

<script>
$(document).ready(
   function(){
      $('#encode').click(function(){
            var source = $("#source").val()// "https://w3schools.com/my test.asp?name=ståle&car=saab";
            var sourceEncoded = btoa(source);//encodeURIComponent(uri); 
            
            $("#encoded").val(sourceEncoded);
      })
      
      $('#decode').click(function(){
                  var sourceEncoded = $("#encoded").val()// "https://w3schools.com/my test.asp?name=ståle&car=saab";
                  //var uri_enc = encodeURIComponent(uri);
                  var uri_dec = atob(sourceEncoded);//decodeURIComponent(uri);
                  
                  $("#source").val(uri_dec);
            })
   }
)
</script>