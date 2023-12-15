---
title: Blog Customization
url: blog-customization
date: 2019-08-19 19:29:00
description: 个人博客自定义样式
categories: Computer Science
tags: [Front-End,Others]
---

## 前言
从大二开始写博客，主要为了记录自己学习过程中的问题。尝试使用过CSDN、博客园等公共服务，也用Github pages搭建过自己的博客，但效果都不令人满意。CSDN广告太多，界面乌烟瘴气，而且很多博客内容都是抄袭而来；博客园模板比较单一，而且对Markdown的支持不友好；Github pages很自由，但是搭建以及发布文章比较麻烦。[blogspot](https://eimadrigal.blogspot.com/)对国内用户很不友好。

后来在网上浏览别人的博客园时，才发现原来是可以自己定制博客的，遂写此文。

## 皮肤
皮肤也就是博客的背景，博客园提供了一些模板，可以在管理->设置->博客皮肤中选择。  
如果你对于CSS比较熟悉，那完全可以自己写一个网页的样式，然后勾选禁用模板默认CSS。  
![img](https://img2018.cnblogs.com/blog/1260581/201908/1260581-20190825184956989-1010438394.png)  
如果你不熟悉Web开发，可以找一些别人写好的页面定制代码，复制到页面定制CSS代码框中。
```css
/*
Monokai Sublime style. Derived from Monokai by noformnocontent http://nn.mit-license.org/
*/

pre {
    /*控制代码不换行*/
    white-space: pre;
    word-wrap: normal;
}

.cnblogs-markdown .hljs {
    display: block;
    overflow: auto;
    padding: 1.3em 2em !important;
    font-size: 16px !important;
    background: #272822 !important;
    color: #FFF;
    max-height: 700px;
}

.hljs,
.hljs-tag,
.hljs-subst {
    color: #f8f8f2;
}

.hljs-strong,
.hljs-emphasis {
    color: #a8a8a2;
}

.hljs-bullet,
.hljs-quote,
.hljs-number,
.hljs-regexp,
.hljs-literal,
.hljs-link {
    color: #ae81ff;
}

.hljs-code,
.hljs-title,
.hljs-section,
.hljs-selector-class {
    color: #a6e22e;
}

.hljs-strong {
    font-weight: bold;
}

.hljs-emphasis {
    font-style: italic;
}

.hljs-keyword,
.hljs-selector-tag,
.hljs-name,
.hljs-attr {
    color: #f92672;
}

.hljs-symbol,
.hljs-attribute {
    color: #66d9ef;
}

.hljs-params,
.hljs-class .hljs-title {
    color: #f8f8f2;
}

.hljs-string,
.hljs-type,
.hljs-built_in,
.hljs-builtin-name,
.hljs-selector-id,
.hljs-selector-attr,
.hljs-selector-pseudo,
.hljs-addition,
.hljs-variable,
.hljs-template-variable {
    color: #e6db74;
}

.hljs-comment,
.hljs-deletion,
.hljs-meta {
    color: #75715e;
}

/* 黑色主题makedown代码结束 */

/*makedown行间代码样式 */
.cnblogs-markdown code {
    color: #c7254e;
    border: none !important;
    font-size: 1em !important;
    background-color: #f9f2f4 !important;
    font-family: sans-serif !important;
}

/*引言样式*/
blockquote {
    border-left: 5px solid #55895B;
}

blockquote strong {
    color: red;
    font-size: 18px;
}

/*博客顶部容器包括标题、副标题、导航栏*/
/* 博客标题和副标题 */
#blogTitle {
    overflow: hidden;
    height: auto;
    text-align: center;
}

#blogTitle h1 {
    font-size: 35px;
    width: 100%;
    margin-left: 0;
}

#blogTitle h2 {
    margin-left: 0;
    width: 100%;
    font-size: 20px;
    font-weight: bold;
    color: #000;
}

/*博客导航栏 */
#navList {
    float: left;
}

#navList li {
    border: none;
    font-size: 16px;
}

.blogStats {
    display: none;
}

/*sideBar博客侧边栏容器*/
#sideBar {
    width: 300px;
    box-sizing: border-box;
    margin-left: 30px;
    padding: 0;
}

.newsItem, .catListComment, .catListEssay, .catListView, .catListFeedback,
#blog-calendar, #sidebar_postcategory, #sidebar_postcategory, #sidebar_postarchive, #sidebar_search {
    border-radius: 10px;
    box-shadow: 1px 2px 3px #A7A8AD;
    background-color: #fff;
}

#sideBarMain h3, .newsItem h3 {
    font-size: 1.2em;
    height: 50px;
    line-height: 50px;
    text-indent: 0.5em;
    background: url(http://www.cnblogs.com/skins/red_autumnal_leaves/images/titlebg.png) no-repeat left center #fff;
    padding: 0 0 0 50px;
    margin-bottom: 0;
    border: 1px solid #55895B;
    border-left-width: 5px;
    border-radius: 10px;
    border-right-width: 5px;
}

#sideBarMain ul {
    background-color: #fff;
    padding: 15px 20px;
    border-bottom-left-radius: 10px;
    border-bottom-right-radius: 10px;
}

#sideBarMain li {
    line-height: 40px;
    border-bottom: 1px solid #ddd;
    font-size: 14px;
}

/*侧边栏公告*/
#blog-news > img {
    /*头像*/
    display: block;
    margin: auto;
    border-radius: 50%;
}

#profile_block {
    font-size: 15px;
    padding: 30px;
    line-height: 1.8;
}

#profile_block > a:link {
    color: #F60;
}

/*公告结束*/


/* 日历 */
#blog-calendar, #calendar {
    width: 300px;
}

#blog-calendar td {
    padding: 5px 3px;
    font-size: 14px;
}

#blog-calendar td a {
    font-weight: bold;
    color: #59a020;
}

#blog-calendar table a:hover {
    color: #59a020;
    text-decoration: underline;
    background: transparent;
}

#blog-calendar table u {
    text-decoration: none;
}

/*日历结束*/

/*设置背景色和字体大小*/

body {
    font-size: 15px;
    box-sizing: border-box;
}

/*mainContent主体内容容器*/
#main {
    display: flex;
    width: 95%;
}

#mainContent .forFlow {
    margin: 0 0 0 310px;
}

#mainContent {
    margin: 0 0 0 -310px;
}

#post_detail {
    overflow: hidden;
}

/* 标题title样式 */

#topics .postTitle {
    font-size: 25px;
    padding: 0 40px;
    border: none;
    box-sizing: border-box;
}

#cb_post_title_url {
    border: 1px solid #55895B;
    border-left-width: 5px;
    border-radius: 10px;
    border-right-width: 5px;
    background-position: left center;
    padding: 15px 50px;
    width: 100%;
    display: inline-block;
    box-sizing: border-box;
}

/* 主体内容样式 */
.postBody {
    padding: 20px 40px;
}

#cnblogs_post_body {
    font-size: 15px;
}

#cnblogs_post_body h2 {
    /*标题h2*/
    border-left: 5px solid #55895B;
    padding: 10px 20px;
    line-height: 2;
    background: #d6dbdf8a;
    margin: 30px 0;
    font-size: 25px;
}

#cnblogs_post_body h3 {
    margin: 20px 0;
    padding: 10px 20px;
    border-left: 5px solid #55895B;
    font-size: 20px;
}

#cnblogs_post_body h4{
    font-size: 18px;
    margin: 20px 0;
}

#topics .postDesc {
    display: none;
}

/* 个性签名 */
#MySignature {
    box-shadow: 8px 1px 10px #989898;
    padding: 10px;
    text-shadow: 1px 1px 1px #FFF;
    font-size: 17px;
    border-left: solid 5px #55895B;
    background: #F3F3F3;
    border-radius: 10px 10px 50% 10px;
    line-height: 2.4;
    margin: 40px 0;
}

#MySignature a {
    text-decoration: none;
    color: #4183c4;
    font-weight: bold;
}

#MySignature a:hover {
    text-decoration: underline;
    color: #f60;
}

#MySignature span {
    color: #f60;
}

/* 关注收藏等几个按钮 */
#green_channel {
    padding: 10px;
    margin: 20px 0;
    font-size: 15px;
    width: 400px;
}

#green_channel a {
    border-radius: 3px;
    text-shadow: none;
    font-weight: normal;
    box-shadow: none;
}

/* 禁用下划线 */
.postBody a:link, .postBody a:visited, .postBody a:active {
    text-decoration: none;
}

/* 上一篇下一篇 */
#post_next_prev {
    font-size: 14px;
    color: #535353;
}

/*底部隐藏作者，隐藏推荐和反对*/
#author_profile {
    display: none;
}

#div_digg {
    display: none;
}

/*隐藏广告*/
#ad_t2, #cnblogs_c1, #under_post_news, #cnblogs_c2, #under_post_kb {
    display: none;
}

/*评论*/
/*评论列表*/
#blog-comments-placeholder {
    border-radius: 10px;
    background: #fff;
    padding: 30px 40px;
}

.feedback_area_title {
    background: url(//www.cnblogs.com/skins/red_autumnal_leaves/images/titlebg.png) no-repeat left center #fff;
    border: 1px solid #55895B;
    border-left-width: 5px;
    border-radius: 10px;
    border-right-width: 5px;
    padding: 15px 50px;
}

/*侧边评论*/
li.recent_comment_body {
    line-height: 30px;
}

/* 提交评论按钮 */
#btn_comment_submit {
    border: solid 1px #fd6d0dd1 !important;
    width: 90px;
    height: 40px;
    color: #fff !important;
    background-color: #fd6d0dd1 !important;
    border-radius: 5px;
    font-size: 16px;
    cursor: pointer;
}
```

## 标题和导航栏
![img](https://img2018.cnblogs.com/blog/1260581/201908/1260581-20190825185903232-1879765133.png)

标题和子标题的修改也在管理->设置中；  
导航栏的控件在管理->选项中勾选，这里还包含侧边栏的控件，可以根据需要自行选择。  
![img](https://img2018.cnblogs.com/blog/1260581/201908/1260581-20190825190124197-141259783.png)

## 侧边栏公告
![img](https://img2018.cnblogs.com/blog/1260581/201908/1260581-20190825190343070-1772796324.png)

这部分的修改也在管理->设置中，不过修改前需要发邮件给博客园后台申请JS权限。  
这里主要有3点：  
一、动态时钟  
这个我是copy了[详谈如何定制自己的博客园皮肤](https://www.cnblogs.com/jingmoxukong/p/7826982.html)；  
二、背景音乐  
背景音乐的添加需要进入网易云音乐网页后，找到喜欢的音乐，生成外链播放器，然后复制那段HTML代码到侧边栏公告即可。  
![img](https://img2018.cnblogs.com/blog/1260581/201908/1260581-20190825191108110-1169449336.png)  
这里要注意：博客园不支持iframe插件，所以只能采用flash插件！  
三、访客统计  
![img](https://img2018.cnblogs.com/blog/1260581/201908/1260581-20190825191224704-336517055.png)  
这个功能可以去[flagcounter](http://www.flagcounter.com/)完成，同样复制HTML代码到侧边栏公告即可。我的博客把这个放到了页脚html代码中，所以可以看到这个在左下角显示。  
完整的博客侧边栏公告代码，注意：其中的网易云音乐和访问人数需要自己生成外链！
```html
<!---  自定义侧边栏  --->
 <div class="mySideBar">
     <p id="p_b_follow"><a href="javascript:void(0);" onclick="follow('ca5022e9-4171-4a38-e168-08d4ef52ecb5')">+Follow Me</a></p>
     <p>student@XJTU</p>
     <p>Email：andrew_ren@163.com</p>
 </div>

 <!--- 动态时钟  --->
<embed wmode="transparent" src="https://files.cnblogs.com/files/jingmoxukong/honehone_clock_tr.swf" quality="high" bgcolor="#FDF6E3" width="240" height="110" name="honehoneclock" align="middle" allowscriptaccess="always" type="application/x-shockwave-flash" pluginspage="http://www.macromedia.com/go/getflashplayer">

 <!--- 网易云音乐  --->
<embed src="//music.163.com/style/swf/widget.swf?sid=26511658&type=2&auto=1&width=320&height=66" width="340" height="86"  allowNetworking="all"></embed>

<!--- 访问人数  --->
<a href="https://info.flagcounter.com/myYT"><img src="https://s01.flagcounter.com/count2/myYT/bg_FFFFFF/txt_000000/border_CCCCCC/columns_2/maxflags_4/viewers_0/labels_1/pageviews_1/flags_0/percent_0/" alt="Flag Counter" border="0"></a>

 <!--- 导入js库  --->
 <script src="//cdn.bootcss.com/canvas-nest.js/1.0.0/canvas-nest.min.js">
 <canvas id="c_n4" width="860" height="968" style="position: fixed; top: 0px; left: 0px; z-index: -1; opacity: 0.5;"></canvas>
```
最后点击保存即可。

## 自适应手机屏幕
博客园的模板并没有自适应手机屏幕，可以参考[这篇博文](https://www.cnblogs.com/lvdabao/p/5245247.html)修改CSS中的参数，就可以得到自适应移动设备的网页。

## Reference
当前使用的是在[这个](https://github.com/Summertime-Wu/make_cnblogs_better)基础上做了一些魔改。