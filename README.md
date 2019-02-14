https://www.cnblogs.com/coco1s/p/8004510.html


扩展程序的消息传递
消息传递存在的必要性是因为内容脚本在网页而不是扩展程序的环境中运行，所以它们通常需要某种方式与扩展程序的其余部分通信。

扩展程序（弹窗页面和后台页面）和内容脚本间的通信使用消息传递的方式。两边均可以监听另一边发来的消息，并通过同样的通道回应。消息可以包含任何有效的 JSON 对象。

 
使用 chrome.* API
消息传递，主要使用了 Chrome 浏览器的内置 chrome 对象进行。打开浏览器，试一下，chrome 对象其实包含了非常多的功能：

image

各种类型的消息传递都是通过这个 chrome 对象进行，分为：

简单的一次性请求
长时间的连接
跨扩展程序消息传递
从网页发送消息
原生消息通信
当然，对于通常而言的普通扩展程序而言，简单的一次性请求就足够我们使用了，举两个例子。

假设我们的 manifest.json 简单定义如下：
# manifest.json
{
    "name": "Url Helper",
    "version": "1.0.0",
    "author": "Coco",
    "manifest_version": 2,
 
    "browser_action": {
        "default_popup": "popup.html"
    },
    "background": {
        "scripts": ["background.js"]
    },
    "content_scripts": [
        {
            "js": ["contentScript.js"]
        }
    ]
}
 
从 Content Script 向 background 事件页面 传递消息
Content Script ，即是注入页面的脚本
# contentScript.js
 
// 发送消息
chrome.runtime.sendMessage(
    {
        msg: '从 Content Script 向 事件页面 传递消息',
        result: 1
    },
    function(response) {
        if (response && response.msg) {
            console.log(response.msg);
        }
    }
);
background 后台页面
#background.js
 
// 接收消息
chrome.runtime.onMessage.addListener(function(request, sender, sendResponse) {
    if (request.result) {
        sendResponse({
            farewell: "ok"
        });
    }
});
在发送端，我们可以使用 runtime.sendMessage 或 tabs.sendMessage 方法。这些方法分别允许您从内容脚本向扩展程序或者反过来发送可通过 JSON 序列化的消息，可选的 callback 参数允许您在需要的时候从另一边处理回应。

而在接收端，我们需要设置一个 runtime.onMessage 事件监听器来处理消息。

 
从 popup 弹窗页面 向 Content Script 传递消息
再举一个翻过来的例子，从 popup 弹窗页面 向 Content Script 传递消息。

popup 弹窗页面
# popup.html 页面内引入的 popup.js
 
let obj = {
    msg: '从 popup 弹窗页面  向 Content Script 传递消息',
    result: 0
};
 
// 发送消息
chrome.tabs.query({ active: true, currentWindow: true }, function(tabs) {
    chrome.tabs.sendMessage(tabs[0].id, obj, function(response) {
        console.log("Send Success");
    });
});
Content Script
# contentScript.js
 
// 接收消息
chrome.runtime.onMessage.addListener(function(request, sender, sendResponse) {
    console.log(sender.tab ? "来自内容脚本：" + sender.tab.url : "来自扩展程序");
 
    if (request && !request.result) {
        console.log(result.msg);
    }
});
这里有个问题需要注意，从 popup 弹窗页面 向 Content Script 传递消息时，由于浏览器可能同时打开多个 tab 页，所以需要指定一下传递的页面，指定发送至哪一个标签页。

使用 chrome.tabs.query({ active: true, currentWindow: true }, function(tabs) {}) 则能正确选中当前打开的标签页。

其他更多的消息传递方式，可以戳这里：消息传递。