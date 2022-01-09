# サイト内リンクページダウンロード

トップページからリンクされているページを巡回して１ページ毎ダウンロードしていくプログラムになります。
まずは３階層までリンクしているHTMLファイルをしらべて、ローカルにHTMLファイルをダウンロードしていきます。

```bash
npm i cheerio-httpcli request
```

getallpagedownloads.js

```javascript
var client = require('cheerio-httpcli');

```
