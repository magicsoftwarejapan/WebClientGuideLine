# カレンダを日本語化する

## 以下のコマンドでモジュールをインストール

npm install moment

npm install @angular/material-moment-adapter@13

## momentのインポート

src\app\material-module.ts に追加する

``` typescript
import 'moment/locale/ja';
```


src\app\magic\各プログラム\ComponentList.component.ts にimportとprovidersを追加する

つまり各プログラム毎に設定が必要

importを追加

``` typescript
import {
    MAT_MOMENT_DATE_FORMATS,
    MomentDateAdapter,
    MAT_MOMENT_DATE_ADAPTER_OPTIONS,
  } from '@angular/material-moment-adapter';
  import { DateAdapter, MAT_DATE_FORMATS, MAT_DATE_LOCALE } from '@angular/material/core';
```

 providers:の中を追加
``` typescript
  @Component({
    selector: 'mga-ComponentList',
    providers: [...magicProviders,
        { provide: MAT_DATE_LOCALE, useValue: 'ja-JP' },
        {
          provide: DateAdapter,
          useClass: MomentDateAdapter,
          deps: [MAT_DATE_LOCALE, MAT_MOMENT_DATE_ADAPTER_OPTIONS],
        },
        { provide: MAT_DATE_FORMATS, useValue: MAT_MOMENT_DATE_FORMATS },
    ],
    templateUrl: './ComponentList.component.html',

})
```