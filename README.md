https://www.cnblogs.com/coco1s/p/8004510.html


��չ�������Ϣ����
��Ϣ���ݴ��ڵı�Ҫ������Ϊ���ݽű�����ҳ��������չ����Ļ��������У���������ͨ����Ҫĳ�ַ�ʽ����չ��������ಿ��ͨ�š�

��չ���򣨵���ҳ��ͺ�̨ҳ�棩�����ݽű����ͨ��ʹ����Ϣ���ݵķ�ʽ�����߾����Լ�����һ�߷�������Ϣ����ͨ��ͬ����ͨ����Ӧ����Ϣ���԰����κ���Ч�� JSON ����

 
ʹ�� chrome.* API
��Ϣ���ݣ���Ҫʹ���� Chrome ����������� chrome ������С������������һ�£�chrome ������ʵ�����˷ǳ���Ĺ��ܣ�

image

�������͵���Ϣ���ݶ���ͨ����� chrome ������У���Ϊ��

�򵥵�һ��������
��ʱ�������
����չ������Ϣ����
����ҳ������Ϣ
ԭ����Ϣͨ��
��Ȼ������ͨ�����Ե���ͨ��չ������ԣ��򵥵�һ����������㹻����ʹ���ˣ����������ӡ�

�������ǵ� manifest.json �򵥶������£�
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
 
�� Content Script �� background �¼�ҳ�� ������Ϣ
Content Script ������ע��ҳ��Ľű�
# contentScript.js
 
// ������Ϣ
chrome.runtime.sendMessage(
    {
        msg: '�� Content Script �� �¼�ҳ�� ������Ϣ',
        result: 1
    },
    function(response) {
        if (response && response.msg) {
            console.log(response.msg);
        }
    }
);
background ��̨ҳ��
#background.js
 
// ������Ϣ
chrome.runtime.onMessage.addListener(function(request, sender, sendResponse) {
    if (request.result) {
        sendResponse({
            farewell: "ok"
        });
    }
});
�ڷ��Ͷˣ����ǿ���ʹ�� runtime.sendMessage �� tabs.sendMessage ��������Щ�����ֱ������������ݽű�����չ������߷��������Ϳ�ͨ�� JSON ���л�����Ϣ����ѡ�� callback ��������������Ҫ��ʱ�����һ�ߴ����Ӧ��

���ڽ��նˣ�������Ҫ����һ�� runtime.onMessage �¼���������������Ϣ��

 
�� popup ����ҳ�� �� Content Script ������Ϣ
�پ�һ�������������ӣ��� popup ����ҳ�� �� Content Script ������Ϣ��

popup ����ҳ��
# popup.html ҳ��������� popup.js
 
let obj = {
    msg: '�� popup ����ҳ��  �� Content Script ������Ϣ',
    result: 0
};
 
// ������Ϣ
chrome.tabs.query({ active: true, currentWindow: true }, function(tabs) {
    chrome.tabs.sendMessage(tabs[0].id, obj, function(response) {
        console.log("Send Success");
    });
});
Content Script
# contentScript.js
 
// ������Ϣ
chrome.runtime.onMessage.addListener(function(request, sender, sendResponse) {
    console.log(sender.tab ? "�������ݽű���" + sender.tab.url : "������չ����");
 
    if (request && !request.result) {
        console.log(result.msg);
    }
});
�����и�������Ҫע�⣬�� popup ����ҳ�� �� Content Script ������Ϣʱ���������������ͬʱ�򿪶�� tab ҳ��������Ҫָ��һ�´��ݵ�ҳ�棬ָ����������һ����ǩҳ��

ʹ�� chrome.tabs.query({ active: true, currentWindow: true }, function(tabs) {}) ������ȷѡ�е�ǰ�򿪵ı�ǩҳ��

�����������Ϣ���ݷ�ʽ�����Դ������Ϣ���ݡ�