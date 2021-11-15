# M5Core2ImageAvatarLitePlus
 ImageAvatarLitePlus for M5Stack Core2 and M5Stack Fire

## M5Core2ImageAvatarLiteとの違い
ImageAvatarLiteは右目のパーツを使って、左目を反転表示させます。したがって、同じ目しか表現できません。ImageAvatarLitePlusではパーツ数が増えますが、左右の目を異なるデザインで表示することが可能です。

# 開発途中です。
ドキュメントも行き届いていません。その点はご了承ください。

# 概要

　あらかじめ用意した画像ファイル（BMP)とJSONファイルの設定を組み合わせてAvatarを作成できるアプリです。
[![M5Core2ImageAvatarLite](https://img.youtube.com/vi/k8I5d31OygU/0.jpg)](https://www.youtube.com/watch?v=k8I5d31OygU)
# 開発環境
- VSCode
- PlatformIO

# 必要なライブラリ
バージョンについては[platformio.ini](platformio.ini)を見てください。
- [LovyanGFX](https://github.com/lovyan03/LovyanGFX)
- [ArduinoJSON](https://github.com/bblanchon/ArduinoJson)
- [ESP32-Chimera-Core](https://github.com/tobozo/ESP32-Chimera-Core)


# 対応機種
 メモリの都合上PSRAMが必要なのでM5Stack Core2とM5Stack Fireのみを対象にしています。
 4bitBMPを使用し、カラーパレットを使用することにより他の機種でも動きますが手順が複雑なのでCore2及びFireのみとします。

# 使い方
1. SDカードのルートにdataにあるフォルダ(bmp,json)をコピー
1. プログラムを書き込むとAvatarが起動します。
1. 口は開閉を繰り返すだけです。BtnCを押すと表情が切り替わります。


## SDカード上に必要なファイル
 
 1. /bmp_slime/<br>BMPファイル(サンプルのbmp_slimeでは全部で11ファイル)<br>
 2. /json/<br>M5AvatarLiteConfig.json<br>M5AvatarLiteServoConfig.json(※サーボを使う場合)

# JSONファイルとBMPファイルの置き場所について
 main.cppの下記の行を変更するとJSONファイルとBMPファイルの収納場所をSDかSPIFFSか指定できます。SPIFFSに置くと開発するときにVSCodeからUploadできるようになり、SDカードを抜き差しして書き換える手間が省けます。
```
fs::FS json_fs = SD; // JSONファイルの収納場所(SPIFFS or SD)
fs::FS bmp_fs  = SD; // BMPファイルの収納場所(SPIFFS or SD)
```
 ## VSCodeからのデータUpload方法（英語）
 [ESP32 with VS Code and PlatformIO: Upload Files to Filesystem (SPIFFS)](https://randomnerdtutorials.com/esp32-vs-code-platformio-spiffs/)

 # カスタマイズ方法
 自分で24bitか16bitのBMPファイルを用意すると好きな画像をAvatar化することが可能です。

 ## 用意する画像ファイル
 サンプルの画像ファイルは全て24bitBMPファイルでWindows標準のペイントソフトを利用しています。
 ### 固定パーツ（頭の画像ファイル）
 1. 頭用BMP<br>背景となる画像ファイル320x240もしくは傾けるのであれば少し余裕が必要です。<br>
 ![image](data/bmp_kaomoji/head.bmp)
 ### 表情で変わるパーツ(目と口)
 開いた状態と閉じた状態の<b>２種類×表情の数</b>だけ必要です。(デザインが同じ場合は流用も可能)
 1. 開いた右目と閉じた右目のパーツ<br>
  ![image](data/bmp_kaomoji/eye_r_op_laugh.bmp) ![image](data/bmp_kaomoji/eye_r_cl_laugh.bmp)
 1. 開いた左目と閉じた左目のパーツ<br>
  ![image](data/bmp_kaomoji/eye_r_op_laugh.bmp) ![image](data/bmp_kaomoji/eye_l_cl_laugh.bmp)
 1. 開いた口と閉じた口のパーツ<br><br>
 ![image](data/bmp_kaomoji/mouth_op_laugh.bmp) ![image](data/bmp_kaomoji/mouth_cl_normal.bmp)

目と口の透明化したい部分は透明色(M5AvatarLiteConfig.json)で塗りつぶします。サンプルでは緑（0x00FF00）になっています。

## JSONファイルの編集
下記を参考にして、JSONファイルを書き換えてください。
``` 
{
    "expression": [  // 表情（デフォルトは最大8パターンまで）
        "normal",
        "laugh",
        "cry",
        "sleepy"
    ],

    "sprite_info": {
        "psram": "true",                     // PSRAMの使用有無(8bit以上ではtrueのまま)
        "color_depth": 16,                   // 使用するBMPのColorDepth
        "swap_bytes": 0                      // Swap_bytes
    },
    "color_info": {
        "skin"  : "0xFF5B00",                // 肌の色(未使用)
        "eye_white" : "0xFFFFFF",            // 白目の色（未使用）
        "transparent"    : "0x00FF00"        // 透明色（sampleでは緑）
    },
    "fixed_parts": [                         // 固定用パーツ（背景のみ）
        {
            "parts": "head",
            "x": 0,                              // 頭部パーツの描画開始X座標
            "y": 0,                              // 頭部パーツの描画開始Y座標
            "w": 320,                            // 頭部パーツの幅
            "h": 240,                            // 頭部パーツの高さ
            "filename": "/bmp_kaomoji/head.bmp"  // 頭部パーツのファイル名
        }
    ],
    "mouth": [                                 // 口のパーツ設定（表情の数だけ必要）
        {
            "normal": {                        // 表情 normal
                "x": 160,                      // 口パーツの描画中心X座標
                "y": 190,                      // 口パーツの描画中心Y座標
                "w": 72,                       // 口パーツの幅
                "h": 72,                       // 口パーツの高さ
                "filename": {
                    "open": "/bmp_kaomoji/mouth_op_normal.bmp",      // 開いた口
                    "close": "/bmp_kaomoji/mouth_cl_normal.bmp"      // 閉じた口
                },
                "minScaleX": 1.0,            // 未使用
                "maxScaleX": 1.0,            // 未使用
                "minScaleY": 0.3,            // Y軸の拡大率がこれ以上小さくなると閉じる
                "maxScaleY": 1.0             // 未使用
            }
        },
        {
            "laugh": {                       // 口のパーツ設定（laugh）
                "x": 160,
                "y": 190,
                "w": 72,
                "h": 72,
                "filename": {
                    "open": "/bmp_kaomoji/mouth_op_laugh.bmp",
                    "close": "/bmp_kaomoji/mouth_cl_normal.bmp"
                },
                "minScaleX": 1.0,
                "maxScaleX": 1.0,
                "minScaleY": 0.3,
                "maxScaleY": 1.0
            }
        },
        {
            "cry": {                         // 口のパーツ設定(cry)
                "x": 160,
                "y": 190,
                "w": 72,
                "h": 72,
                "filename": {
                    "open": "/bmp_kaomoji/mouth_op_cry.bmp",
                    "close": "/bmp_kaomoji/mouth_cl_cry.bmp"
                },
                "minScaleX": 1.0,
                "maxScaleX": 1.0,
                "minScaleY": 0.3,
                "maxScaleY": 1.0
            }
        },
        {
            "sleepy": {                     // 口のパーツ設定(sleepy)
                "x": 160,
                "y": 190,
                "w": 72,
                "h": 72,
                "filename": {
                    "open": "/bmp_kaomoji/mouth_op_sleepy.bmp",
                    "close": "/bmp_kaomoji/mouth_cl_normal.bmp"
                },
                "minScaleX": 1.0,
                "maxScaleX": 1.0,
                "minScaleY": 0.3,
                "maxScaleY": 1.0
            }
        }
    ],
    "eye_r": [                                // 右目パーツの設定（設定内容は口と一緒）
        {
            "normal": {
                "x": 80,
                "y": 120,
                "w": 72,
                "h": 72,
                "filename": {
                    "open": "/bmp_kaomoji/eye_r_op_normal.bmp",
                    "close": "/bmp_kaomoji/eye_r_cl_normal.bmp"
                },
                "minScaleX": 1.0,
                "maxScaleX": 1.0,
                "minScaleY": 0.3,
                "maxScaleY": 1.0
            }
        },
        {
            "laugh": {
                    "x": 80,
                    "y": 120,
                    "w": 72,
                    "h": 72,
                    "filename": {
                        "open": "/bmp_kaomoji/eye_r_op_laugh.bmp",
                        "close": "/bmp_kaomoji/eye_r_cl_laugh.bmp"
                    },
                    "minScaleX": 1.0,
                    "maxScaleX": 1.0,
                    "minScaleY": 0.3,
                    "maxScaleY": 1.0
            }
       },
       {
            "cry": {
                    "x": 80,
                    "y": 120,
                    "w": 72,
                    "h": 72,
                    "filename": {
                        "open": "/bmp_kaomoji/eye_r_op_cry.bmp",
                        "close": "/bmp_kaomoji/eye_r_cl_cry.bmp"
                    },
                    "minScaleX": 1.0,
                    "maxScaleX": 1.0,
                    "minScaleY": 0.3,
                    "maxScaleY": 1.0
            }
        },
       {
            "sleepy": {
                    "x": 80,
                    "y": 120,
                    "w": 72,
                    "h": 72,
                    "filename": {
                        "open": "/bmp_kaomoji/eye_r_op_sleepy.bmp",
                        "close": "/bmp_kaomoji/eye_r_cl_normal.bmp"
                    },
                    "minScaleX": 1.0,
                    "maxScaleX": 1.0,
                    "minScaleY": 0.3,
                    "maxScaleY": 1.0
            }
        }
    ],
    "eye_l": [                                                //　左目の設定（設定内容は口と一緒）
        {
            "normal": {
                "x": 240,
                "y": 120,
                "w": 72,
                "h": 72,
                "filename": {
                    "open": "/bmp_kaomoji/eye_r_op_normal.bmp",
                    "close": "/bmp_kaomoji/eye_r_cl_normal.bmp"
                },
                "minScaleX": 1.0,
                "maxScaleX": 1.0,
                "minScaleY": 0.3,
                "maxScaleY": 1.0
            }
        },
        {
            "laugh": {
                "x": 240,
                "y": 120,
                "w": 72,
                "h": 72,
                "filename": {
                    "open": "/bmp_kaomoji/eye_r_op_laugh.bmp",
                    "close": "/bmp_kaomoji/eye_l_cl_laugh.bmp"
                },
                "minScaleX": 1.0,
                "maxScaleX": 1.0,
                "minScaleY": 0.3,
                "maxScaleY": 1.0

            }
       },
        {
            "cry": {
                "x": 240,
                "y": 120,
                "w": 72,
                "h": 72,
                "filename": {
                    "open": "/bmp_kaomoji/eye_r_op_cry.bmp",
                    "close": "/bmp_kaomoji/eye_r_cl_cry.bmp"
                },
                "minScaleX": 1.0,
                "maxScaleX": 1.0,
                "minScaleY": 0.3,
                "maxScaleY": 1.0
            }
        },
        {
            "sleepy": {
                "x": 240,
                "y": 120,
                "w": 72,
                "h": 72,
                "filename": {
                    "open": "/bmp_kaomoji/eye_r_op_sleepy.bmp",
                    "close": "/bmp_kaomoji/eye_r_cl_normal.bmp"
                },
                "minScaleX": 1.0,
                "maxScaleX": 1.0,
                "minScaleY": 0.3,
                "maxScaleY": 1.0
            }
        }
    ],
    "init_param": [                          // 最初に描画されるときの設定（特に変更の必要はありませんが表情の数必要です。）
        {
            "normal": {
                "eye_l_ratio": 0.0,          // 左目を開く倍率
                "eye_r_ratio": 0.0,          // 右目を開く倍率
                "mouth_ratio": 0.0,          // 口を開く倍率
                "angle": 0.0,                // Avatarの角度（左右に傾きます）
                "breath": 0                  // Avatarが息遣いする高さ
            }
        },
        {
            "laugh": {
                "eye_l_ratio": 0.0,
                "eye_r_ratio": 0.0,
                "mouth_ratio": 0.0,
                "angle": 0.0,
                "breath": 0
            }
        },
        {
            "cry": {
                "eye_l_ratio": 0.0,
                "eye_r_ratio": 0.0,
                "mouth_ratio": 0.0,
                "angle": 0.0,
                "breath": 0
            }
        },
        {
            "sleepy": {
                "eye_l_ratio": 0.0,
                "eye_r_ratio": 0.0,
                "mouth_ratio": 0.0,
                "angle": 0.0,
                "breath": 0
            }
        }
    ]
}
```

## カスタマイズ例
[avatar_data](avatar_data)フォルダにいくつか作例を上げていきます。（表情の数はバラバラです。）

# 参考にしたリポジトリ
- [m5stack-avatar](https://github.com/meganetaaan/m5stack-avatar)
- [M5Stack_WebRadio_Avator](https://github.com/robo8080/M5Stack_WebRadio_Avator)

# サーボについて
main.cpp冒頭の#define USE_SERVOのコメントを外すとサーボを利用できます。2021/11現在では[ｽﾀｯｸﾁｬﾝ](https://github.com/meganetaaan/stack-chan)での利用を想定しています。

## サーボの初期設定
X軸とY軸の2軸（ｽﾀｯｸﾁｬﾝのパン(x)とチルト(y)）で利用できます。/json/フォルダにM5AvatarLiteServoConfig.jsonを置いてください。

[![M5Core2ImageAvatarLite](https://img.youtube.com/vi/hMfaPWbj1Ps/0.jpg)](https://www.youtube.com/watch?v=hMfaPWbj1Ps)


## M5AvatarLiteServoConfig.jsonの内容
```
{
    "initial_settings": {
        "x_axis": {            // パン
            "pin"    : 13,     // 水平方向のサーボピン番号
            "center" : 85,     // サーボの中心（初期位置）
            "lower"  : 0,      // サーボの下限角度
            "uppder"  : 180     // サーボの上限角度
        },
        "y_axis": {            // チルト
            "pin"    : 14,     // 垂直方向のサーボ品番号
            "center" : 60,     // サーボの中心(初期位置)
            "lower"  : 30,     // サーボの下限角度
            "upper"  : 90      // サーボの上限角度
        }

    },
    "servo_enable" : "false"   // 起動時にサーボを動かすかどうかのフラグ（未使用）
}
```

# 謝辞
このソフトを作成するにあたり、動きや構造の元となった[M5Stack-Avatar](https://github.com/meganetaaan/m5stack-avatar)を作成・公開してくださった[meganetaaan](https://github.com/meganetaaan)氏に感謝いたします。

ImageAvatarを実現するにあたり優れたパフォーマンス、機能を持ったLovyanGFXの作者[lovyan03](https://github.com/lovyan03)氏に感謝いたします。

ImageAvatar作成するにあたり、 初期の頃からたくさんのアドバイスを頂き、参考にさせていただいた[M5Stack_WebRadio_Avatar](https://github.com/robo8080/M5Stack_WebRadio_Avator)の作者[robo8080](https://github.com/robo8080)氏に感謝いたします。

作成後に、メモリの使い方等アドバイスをくれた[tobozo](https://github.com/tobozo)氏に感謝いたします。


# Credit
- [meganetaaan](https://github.com/meganetaaan)
- [lovyan03](https://github.com/lovyan03/LovyanGFX)
- [robo8080](https://github.com/robo8080)
- [tobozo](https://github.com/tobozo)
# LICENSE
[MIT](LICENSE)

# Author
[Takao Akaki](https://github.com/mongonta0716)
