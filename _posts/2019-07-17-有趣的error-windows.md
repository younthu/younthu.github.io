---
layout: post
title: 有趣的Error Windows
excerpt: 搜集一些有趣的系统出错页面
---
搜集日常生活中遇到的各种终端设备出错页面。

# 地铁刷卡机出错

2019年7月17日，在2号线南京西路地铁站出地铁的时候遇到机器黑屏，刷不了Metro大都会.

<div class="bd-example">
  <div id="carouselExampleCaptions" class="carousel slide" data-ride="carousel">
    <ol class="carousel-indicators">
      <li data-target="#carouselExampleCaptions" data-slide-to="0" class="active"></li>
      <li data-target="#carouselExampleCaptions" data-slide-to="1"></li>
      <li data-target="#carouselExampleCaptions" data-slide-to="2"></li>
    </ol>
    <div class="carousel-inner">
      <div class="carousel-item active">
        <img src="{{site.assets}}/img/error_windows/2019.07.17.IMG_1548.JPG" class="d-block w-100" alt="...">
        <div class="carousel-caption d-none d-md-block">
          <h5>刷卡机黑屏</h5>
          <p>提示文字: EMM386 has detected error #12 in an application at memory address 06FB:3790. To minimize the chance of data loss, EMM386 has halted your computer. For more information, see the READEME.TXT file.

    <br/>
    <br/>
    To restart your computer, press ENTER.
          </p>
        </div>
      </div>
      <div class="carousel-item">
        <img src="{{site.assets}}/img/error_windows/2019.07.17.IMG_1551.JPG" class="d-block w-100" alt="...">
        <div class="carousel-caption d-none d-md-block">
          <h5>正常的刷卡机</h5>
          <p>正常情况下屏幕会显示很简单的几个大文字</p>
        </div>
      </div>
    </div>
    <a class="carousel-control-prev" href="#carouselExampleCaptions" role="button" data-slide="prev">
      <span class="carousel-control-prev-icon" aria-hidden="true"></span>
      <span class="sr-only">Previous</span>
    </a>
    <a class="carousel-control-next" href="#carouselExampleCaptions" role="button" data-slide="next">
      <span class="carousel-control-next-icon" aria-hidden="true"></span>
      <span class="sr-only">Next</span>
    </a>
  </div>
</div>


# 联合投影机出错

2019年7月20日，浦东某温泉室内投影机出错。

该投影屏幕是一个超宽墙面银幕，由两台投影仪融合投影，可以看到对话框融合的不好.双投影仪融合投影时中间合并处会有重叠部分，下面图片中的亮条就是两台投影机重叠的地方，该融合器未做融合部分的亮度减弱出来，所以重叠部分格外亮。

仔细看可以发现对话框部分在亮条部，由两台投影机一起投射，文字对不齐了。上面的白云在边界层也不怎么对的上号。

从出错对话框里的提示可以看出来系统已经检测出投影重合部分有问题了。

<div class="bd-example">
  <div id="carouselExampleCaptions" class="carousel slide" data-ride="carousel">
    <ol class="carousel-indicators">
      <li data-target="#carouselExampleCaptions" data-slide-to="0" class="active"></li>
      <li data-target="#carouselExampleCaptions" data-slide-to="1"></li>
      <li data-target="#carouselExampleCaptions" data-slide-to="2"></li>
    </ol>
    <div class="carousel-inner">
      <div class="carousel-item active">
        <img src="{{site.assets}}/img/error_windows/2019.07.20.IMG_1589.JPG" class="d-block w-100" alt="...">
        <div class="carousel-caption d-none d-md-block">
          <h5>联合投影器出错</h5>
          <p>提示文字: 扫描不见可能没有回复到其原来位置。
          </p>
          <p>可能是投影系统检测到中间融合部分出问题了</p>
        </div>
      </div>
      <div class="carousel-item active">
        <img src="{{site.assets}}/img/error_windows/2019.07.20.IMG_1590.JPG" class="d-block w-100" alt="...">
        <div class="carousel-caption d-none d-md-block">
          <h5>联合投影器出错</h5>
          <p>提示文字: 扫描不见可能没有回复到其原来位置。
          </p>
          <p>可能是投影系统检测到中间融合部分出问题了</p>
        </div>
      </div>
    </div>
    <a class="carousel-control-prev" href="#carouselExampleCaptions" role="button" data-slide="prev">
      <span class="carousel-control-prev-icon" aria-hidden="true"></span>
      <span class="sr-only">Previous</span>
    </a>
    <a class="carousel-control-next" href="#carouselExampleCaptions" role="button" data-slide="next">
      <span class="carousel-control-next-icon" aria-hidden="true"></span>
      <span class="sr-only">Next</span>
    </a>
  </div>
</div>

# 上海地铁测试通知上了生产环境

2019年7月23日，上海地铁app ”大都会“发了一个测试push notification到我手机上。

<div class="bd-example">
  <div id="carouselExampleCaptions" class="carousel slide" data-ride="carousel">
    <ol class="carousel-indicators">
      <li data-target="#carouselExampleCaptions" data-slide-to="0" class="active"></li>
      <li data-target="#carouselExampleCaptions" data-slide-to="1"></li>
      <li data-target="#carouselExampleCaptions" data-slide-to="2"></li>
    </ol>
    <div class="carousel-inner">
      <div class="carousel-item active">
        <img src="{{site.assets}}/img/error_windows/2019.07.23.IMG_1595.JPG" class="d-block w-100" alt="...">
        <div class="carousel-caption d-none d-md-block">
          <h5>一串莫名其妙的数字</h5>
          <p>看着像是在生产环境上做测试。
          </p>
          <p>点开通知，进入到了地铁线路列表.</p>
        </div>
      </div>
  </div>
</div>


