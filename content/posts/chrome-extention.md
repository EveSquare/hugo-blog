---
title: "Chrome Extention"
date: 2021-12-16T09:10:50+09:00
draft: true
---

# Web extension intercepting request

### やりたかったこと
GETリクエストのResponseBodyを取得したい．
![chrome devtools](https://raw.githubusercontent.com/EveSquare/images/main/%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202021-12-14%2016.28.49.png?token=AMA45GUE2M5AHV37VB6JJDTBYGBPE)

候補として以下の3つがヒットした．
- <a href="# × webRequest.onBeforeRequest.addListener">webRequest.onBeforeRequest.addListner</a>
- <a href="# × devtools.network.onRequestFinished.addListener">devtools.network.onRequestFinished.addListener</a>
- <a href="# × monkey patch">mokey patch</a>

結論として全て不可能であった．

## × webRequest.onBeforeRequest.addListener

extensionが扱える関数でリクエストをブロックするなどの利用用途がある．
このリスナーで受け取れるデータは，URLなどのヘッダ情報のみであった．

実際に動かしたプログラム

```
sample-extension/
    backgtound.js
    manifest.json
```


```javascript
//background.js
chrome.webRequest.onBeforeRequest.addListener(
    request_callback,
    {
        urls: ['<all_urls>'],
        types: ['xmlhttprequest'],
    },
    ["blocking", "requestBody"]
);

function request_callback(details) {
    if (details.method === "GET" && details.type === "xmlhttprequest") {
        if (details.url.match(MER_URL_PATTERN)) {
            console.log(details.requestBody); //undefined
        }
    }
}
```

```json
//manifest.json
{
    "manifest_version": 2,
    "name": "Extension",
    "version": "0.0.0",
    "permissions": [
      "<all_urls>",
      "webRequest",
      "webRequestBlocking",
    ],
    "background": {
      "scripts": [
        "background.js"
      ]
    }
}
```

backgroundコンソール(Chromeの拡張機能から確認できる)にBodyが表示されているはず．

# × <a href="https://developer.chrome.com/docs/extensions/reference/devtools_network/">devtools.network.onRequestFinished.addListener</a>

[documentation](https://developer.chrome.com/docs/extensions/reference/devtools_network/)

Chromeのdevelop toolを開いた状態でないと実行されないらしい．
`devtools`を使用する場合は`js`ファイルを読み込むHTMLファイルを用意する必要がある．

```
sample-extension2/
    devtools.js
    devtools.html
    manifest.json
```

```javascript
//devtools.js
function handleRequestFinished(request) {
        chrome.devtools.inspectedWindow.eval(`console.log('${request.request.url}');`);
        request.getContent(function(content) {
            chrome.devtools.inspectedWindow.eval(`console.log('${content}');`);
        });
    }
}

chrome.devtools.network.onRequestFinished.addListener(handleRequestFinished);
```

```html5
<!-- devtools.html -->
<html>
    <head>
        <script src="devtools.js"></script>
    </head>
    <body></body>
</html>
```

```json
//manifest.json
{
    "name": "Extension",
    "version": "1.0",
    "manifest_version": 2,
    "devtools_page": "devtools.html",
    "permissions": [
        "<all_urls>"
    ]
}

```

POSTのデータはBodyを確認することが出来たが，GETレスポンスのBodyは値が無かった．

# × monkey patch

散見された記事によると`window.XMLHttpRequest.prototype.open`を書き換えることでinterceptできるらしい．
うまくいかず．


##### 以下ソース
> [How can I capture all network requests and full response data when loading a page in Chrome?](https://stackoverflow.com/questions/52969381/how-can-i-capture-all-network-requests-and-full-response-data-when-loading-a-pag)

> [Chrome Extension: Reading the BODY of an HTTP response object](https://betterprogramming.pub/chrome-extension-intercepting-and-reading-the-body-of-http-requests-dd9ebdf2348b)

> [Chrome Extension - How to get HTTP Response Body?
](https://stackoverflow.com/questions/18534771/chrome-extension-how-to-get-http-response-body)