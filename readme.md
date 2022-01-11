# サイト内リンクページダウンロード

トップページからリンクされているページを巡回して１ページ毎ダウンロードしていくプログラムになります。
まずは３階層までリンクしているHTMLファイルをしらべて、ローカルにHTMLファイルをダウンロードしていきます。

```bash
npm i cheerio-httpcli request
```

getallpagedownloads.js

```javascript
var client = require('cheerio-httpcli');
var request = require('request');
var URL = require('url');
var fs = require('fs');
var path = require('path');
// 階層の指定
var LINK_LEVEL = 3;
// 基準となるページURL
var TARGET_URL = "https://nodejs.org/dist/latest-v17.x/docs/api/";
var list = {};
// ダウンロード関数実行
download2rec(TARGET_URL, 0);


function download2rec(url, level) {
    // 最大レベルチェック
    if (level >= LINK_LEVEL) return;
    // 重複ダウンロードチェック
    if (list[url]) return;
    list[url] = true;
    // 基準ページ以外なら無視する
    var us = TARGET_URL.split("/");
    us.pop();
    var base = us.join("/");
    if (url.indexOf(base) < 0) return;
    // HTMLを取得する
    client.fetch(url, {}, function (err, $, res) {
        // リンクされているページを取得
        $("a").each(function (idx) {
            // <a> リンク先を取得する
            var href = $(this).attr("href");
            if (!href) return;
            // 絶対パスを相対パスに変換
            href = URL.resolve(url, href);
            // # 以降のURIを無視する
            href = href.replace(/\#.*$/, "");
            //console.log(href.indexOf(base));
            download2rec(href, level + 1);
        });
        // ページを保存ファイル名を決める
        if (url.substr(url.length - 1, 1) == "/") {
            url += "index.html";
        }
        var savepath = url.split("/").slice(2).join("/");
        console.log(savepath);
        checkSave2Dir(savepath);
        fs.writeFileSync(savepath, $.html());
    });
}

/**
 * ディレクトリ作成処理
 * @param {String} fname 
 */
function checkSave2Dir(fname) {
    // 保存先ディレクトリ名取得
    var dir = path.dirname(fname);
    // ディレクトリを再帰的に作成する
    var dirlist = dir.split("/");
    var p = "";
    for (var i in dirlist) {
        p += dirlist[i] + "/";
        if (!fs.existsSync(p)) {
            fs.mkdirSync(p);
        }
    }
}

```

以下のコマンドを実行するとページをすべてダウンロードします

```bash
node getallpagedownloads.js
```
