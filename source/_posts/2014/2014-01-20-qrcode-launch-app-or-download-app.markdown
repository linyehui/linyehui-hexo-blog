---
title: '使用二维码启动应用或下载应用'
layout: post
tags:
---

#### 需求：

用手机拍下二维码，调用起应用，如果应用没有安装就提示下载应用

#### 解决方案：

手机淘宝就是个现成的例子

1、手机淘宝的原始二维码链接：<http://ma.taobao.com/311fY>  

2、跳转后：<http://h5.m.taobao.com/1212/express-pcqr.html?_qr=http%3A%2F%2Fma.taobao.com%2F311fY>  

3、查看这个页面的代码你能直接发现实现的原理


    <!DOCTYPE HTML> <html> <head> <meta charset="utf-8"/> <meta content="yes" name="apple-mobile-web-app-capable"/> <meta content="yes" name="apple-touch-fullscreen"/> <meta content="telephone=no,email=no" name="format-detection"/> <link rel="apple-touch-icon" href="favicon.png"/> <link rel="Shortcut Icon" href="favicon.png" type="image/x-icon"/> <title>手机淘宝购物送刮免单</title> </head> <body><script type="text/javascript">
    with(document)with(body)with(insertBefore(createElement("script"),firstChild))setAttribute("exparams","req_url=http%3a%2f%2fh5%2em%2etaobao%2ecom%2f1212%2fexpress%2dpcqr%2ehtml&amp;category=&amp;userid=&amp;b2c_orid=&amp;b2c_auction=&amp;at_isb=&amp;atp_isdpp=&amp;at_ssid=&amp;bbid=&amp;aplus&amp;at_cart=&amp;at_udid=",id="tb-beacon-aplus",src=(location>"https"?"//s":"//a")+".tbcdn.cn/s/aplus_wap.js")
    </script>
     <script src="http://g.tbcdn.cn/mtb/lib-smartbanner/0.3.1/function.js"></script> <script src="http://g.tbcdn.cn/mtb/lib-httpurl/1.0.0/httpurl.js"></script> <script>var smartbanner=lib.smartbanner;var HttpUrl=lib.httpurl();var redirect="http://m.taobao.com/go/act/scrapoutfree.html";var param=new HttpUrl(location.href);var ttid=param.search&&param.search.ttid;if(smartbanner.ttidInTaobaoApp()||smartbanner.uaInTaobaoApp()){if(ttid){redirect+="?ttid=";redirect+=ttid}location.href=redirect}else{redirect="taobaowebview://m.taobao.com/?weburl="+encodeURIComponent(redirect);var sbInstance=smartbanner.getInstance({href:redirect,url:"http://m.taobao.com/go/act/other/1212pcwuliuerweima.html",crossplat:true});sbInstance&&sbInstance.install()};</script> </body> </html>


**核心的JS文件是：<http://g.tbcdn.cn/mtb/lib-smartbanner/0.3.1/function.js>  **


    !function(a,b){function c(){var a={},b=location.search.slice(1).split("&");if(b[0].length)for(var c=0;c<b.length;c++){var d=b[c].split("=");a[d[0]]=d[1]}return a}function d(a){a&&a.apuri&&(a.ap_uri=a.apuri,delete a.apuri);var b=a||{},c=g.getElementById("buried");ttid=c&&c.value,href=location.href.toString(),ttid&&-1==href.indexOf("ttid")&&(href.indexOf("?")>-1?href=href.replace("?","?ttid="+ttid):href.indexOf("#")>-1?href=href.replace("#","?ttid="+ttid+"#"):href+="?ttid="+ttid),b.ap_ref=encodeURIComponent(href),h&&h.length>1&&(b.ap_cna=h[1]);var d=[];for(var e in b)d.push(e+"="+encodeURIComponent(b[e]));var f=g.createElement("script");f.src="http://log.m.taobao.com/js.do?"+d.join("&").replace(/%20/g,"+"),g.body.appendChild(f)}function e(){var a=c(),b=a.ttid,d=/.+@taobao_(iphone|android|apad|ipad)_.+/i;return b=b?decodeURIComponent(b):"",d.test(b)}function f(){var a=navigator.userAgent;return null!=a.match(/WindVane/i)?!0:!1}var g=a.document,h=g.cookie.match(/(?:^|\s)cna=([^;]+)(?:;|$)/),i=g.createElement("frame"),j=function(a){var b=this,c=navigator.standalone,d=navigator.userAgent;return null!=d.match(/iPhone|iPod|iPad/i)?(this.platform="ios",this.isIpad=null!=d.match(/iPad/i)):null!=d.match(/Android/i)?null!=d.match(/Mobile/i)&&(this.platform="android",this.isChrome=null!=d.match(/Chrome/i)&&null==d.match(/Version\/\d+\.\d+(\.\d+)?\sChrome\//i)):null!=d.match(/Linux/i)&&(this.platform="android"),!this.platform||c?(this.invaliable=!0,null):(this.init(a)&&(this.create(),window.onblur=function(){clearTimeout(b.timeload),b.timeload=null}),this)};j.prototype={constructor:j,init:function(a){var b=this.options=a,d=b.isInstance||function(){return f()||e()};if(d())return this.invaliable=!0,null;a.version||(a.version="v1"),this.cover=b.cover||!1,this.isDownload=b.download||!1,this.timeout=b.timeout||600;var h=b.from||"h5",i=b.crossplat||!1;if(this.bannerUrl="ios"!=this.platform||i?b.url||"http://m.taobao.com/channel/act/sale/tbdl1.html":b.appstoreUrl||(this.isIpad?"https://itunes.apple.com/app/id438865278":"http://itunes.apple.com/cn/app/id387682726?mt=8"),b.href){var j=b.href,k=c(),l=g.getElementById("buried"),m=k.ttid||l&&l.value,n=k.sprefer,o=k.ali_trackid,p=location.href;p=-1!=p.indexOf("?")?p.split("?")[0]:p,p=-1!=p.indexOf("#")?p.split("#")[0]:p;var q={from:h,url:encodeURIComponent(p)},r=b.point;if(r)for(var s in r)q[s]=r[s];m&&(q.ttid=m),n&&(q.sprefer=n),o&&(q.ali_trackid=o),q.ap_uri="",q=encodeURIComponent(JSON.stringify(q));var t,u=j.indexOf("#");if(-1!=u&&(t=j.slice(u),j=j.replace(t,"")),-1==j.indexOf("?")?j+="?":j.indexOf("?")!=j.length-1&&(j+="&"),j+="point="+q+(t?t:""),j=-1!=j.indexOf("://")?j:"taobao://"+j,this.isChrome){var v=j.split("://"),w=v[0],x=v[1],y=b.package||"com.taobao.taobao";j="intent://"+x+"#Intent;scheme="+w+";package="+y+";end"}this.paramUrl=j}return!0},reset:function(a){this.iClose||(this.init(a),this.resetHtml&&this.resetHtml(a))},create:function(){this.iClose||(i.parentNode||(i.setAttribute("id","J_smartFrame"),i.style.cssText="display:none",g.body.appendChild(i)),this.frame=i)},download:function(b){var c=Date.now();(!b||c-b<this.timeout+200)&&(this.cover?a.location.replace(this.bannerUrl):a.location.href=this.bannerUrl)},redirect:function(b){var c=this.options&&this.options.version,e=this.frame,f=b?"click_sb_"+c+"_manual":"click_sb_"+c+"_auto";this.paramUrl&&(d({ap_uri:f}),this.paramUrl=this.paramUrl.replace("%22ap_uri%22%3A%22%22",encodeURIComponent('"ap_uri":"'+f+'"')),this.isChrome?a.location.href=this.paramUrl:e&&e.setAttribute("src",this.paramUrl))},install:function(a){var b=this,c=Date.now();b.isDownload||(b.timeload=setTimeout(function(){b.download(c)},b.timeout)),b.redirect(a)}},b.smartbanner=function(a){var c=a.type,d=b.smartbanner.BannerUI,e=b.smartbanner.PopUI;if("banner"!==c&&c){if("pop"===c){if(e)return new e(a)}else if("func"===c)return b.smartbanner.getInstance(a)}else if(d)return new d(a)},b.smartbanner.getInstance=function(a,b){b||(b=Object.create({}));for(var c in j.prototype)b[c]=j.prototype[c];return j.call(b,a)},b.smartbanner.aplus=d,b.smartbanner.getParam=c,b.smartbanner.ttidInTaobaoApp=e,b.smartbanner.uaInTaobaoApp=f}(window,window.lib||(window.lib={}));


看了下，github上有这个库：
[jquery.smartbanner](https://github.com/jasny/jquery.smartbanner)


有现成的方案可以参考真好 :)