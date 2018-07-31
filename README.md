# DOCSS（仮）
DOCSS（仮）は[OOCSS](https://github.com/stubbornella/oocss/wiki)、[SMACSS](https://smacss.com/ja)、[BEM](http://bem.info/)の基本的な考え方を参考に[FLOCSS](https://github.com/hiloki/flocss)に大きな影響を受けたCSSのコーディングルールです。  
自分のような1人でコーディングをする、**比較的小規模なサイト**制作時のルールです。

## 基本ルール
* レイヤーは**Base**、**Layout**、**PageBlock**、**Module**、**Component**、**Utility**で構成
* 下位レイヤーが上位レイヤーを上書きすることは禁止します
* IDはLayout、javascriptのみ使用可能、**javascriptの場合スタイルは当てない**
* SassやPostcssのネストは極力**1つ**まで
* Sass等でファイルを分ける場合はレイヤーごとに分ける
* レイヤーにはプレフィックスをつける
* 基本的に全部にクラスを付けるが、最下位層の要素に関してはクラスは不要とする
* ルールは厳格に守ろうとせず、難しい場合は諦める事も必要

## 命名規則
FLOCSSの命名規則のように[MindBEMding](https://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)を採用し、ElementのElementはキャメルケースで記述します。  
Stateパターンの命名もFLOCSSのようにjavascriptでの状態変化を表したい時に`is-`プレフィックスで記述します。  
ただし、javascript以外、**Modifierとしても使用します。**(--が冗長なのが否めないため)
ElementｎElementは**2つ**までとし、それ以降はプレフィックスを付けない新しいクラスを当ててそこからElementを続けていきます。  

    /* HTML */
    <div class="cm-block">
        <div class="cm-block__el">
            <div class="cm-block__elEl">
                <div class="cm-block__elElEl">
                    <dl class="block">
                        <dt class="block__title">...</dt>
                        <dd class="block__body">
                            <p class="block__bodyEl">
                                <span class="block__bodyElEl"></span>
                            </p>
                        </dd>
                    </dl>
                </div>
            </div>
        </div>
    </div>

    /* CSS */
    .cm-block { ... }
    .cm-block__el { ... }
    .cm-block__elEl { ... }
    .cm-block__elElEl { ... }
    .cm-block__elElEl .block { ... }
    .cm-block__elElEl .block__title { ... }
    .cm-block__elElEl .block__bodyEl { ... }
    .cm-block__elElEl .block__bodyElEl { ... }

## レイヤー
### Base
`Reset.css`や`Normalize.css`を使用し、サイトのスタイルの基礎部分を構築します。  
制作するサイトによって変更する部分はベースとなる背景色・文字色・フォントの種類等になります。

### Layout
ヘッダーやフッター等、ページ全体の共通レイアウトの大枠になる部分です。  
スタイルの記述は許可します。  
IDセレクタを使用しページ内で1つしかない要素として定義します。  
javascriptから直接参照することも出来ます。  
BEMでいうElementやModifierは禁止し、内包する要素を上書きすることも禁止します。  
プレフィックスとして`l-`をつけます。

    #l-wrapper { ... }
    #l-header { ... }
    #l-main { ... }
    #l-footer { ... }

    ☓ #l-wrapper .p-top { ... }
    ☓ #l-wrapper__block { ...}


### PageBlock
ページ固有のブロックのスタイルを定義します。  
ページ共通のスタイルは後述のModuleで記述します。  
プレフィックスとして`p-pageslug`とし、Elementで内包ブロックを記述します。

例)トップページの場合  

    <div class="p-top">
        <div class="p-top__main">
            <div class="p-top__mainBlock">
            </div>
        </div><!-- main block -->
        <div class="p-top__sub">
            <div class="p-top__subBlock">
            </div>
        </div><!-- sub block -->
    </div>

**PageBlock内での上書きは禁止します**  

    ☓ .p-top .p-top__main { ... }

### Module
Moduleには2種類あります。  
Pageblockで上書きすることは許可します。

#### PageModule
ページ内で共通の要素を定義します。  
プレフィックスは`pm-pageslug`とし、Elementで内包要素を記述します。

例)トップページの場合

    <div class="p-top">
        <div class="p-top__main pm-top__block">
            <div class="pm-top__blockEl">
            </div>
        </div>
        <div class="p-top__sub pm-top__block">
            <div class="pm-top__blockEl">
            </div>
        </div>
    </div>

#### CommonModule
複数ページに共通の要素を定義します。  
プレフィックスは`cm-`とし、Elementで内包要素を記述します。

    <div class="cm-block">
        <div class="cm-block__el">
            <div class="cm-block__elEl">
            </div>
        </div>
    </div>

### Component
最も小さい単位のパーツとしての要素です。  
プレフィックスは`c-`とします。  
ボタンの色違いなどはModifierで対応することとします。  
ただし、幅や高さマージン等、限定的になる記述はせず、共通幅のボタンなどはCommonModuleやPageblockで上書きするようにします。

    <div class="p-top__btn">
        <button class="c-btn is-green"></button>
    </div>

    /* Compornent */
    .c-btn { ... }
    .c-btn.is-green { ... }

    /* PageBlock */
    .p-top__btn .c-btn { ... }

### Utility
Compornentのようなパーツではなく、汎用的に使用するスタイルを記述する要素です。  
プレフィックスは`u-`とし、Elementは禁止します。  
CommonModuleやPageblockでの上書きは許可します。

    .u-cf { ... } /* clearfix */
    .u-section { max-width: 1000px; width: 93.75%; margin: 0 auto; }
    .u-section.is-wide { max-width: 1280px; }


## PageBlockやModuleのModifier

    <div class="p-top__block">
        <div class="p-top__blockItem is-01">
        </div>
        <div class="p-top__blockItem is-02">
        </div>
        <div class="p-top__blockItem is-03">
        </div>
        <div class="p-top__blockItem is-04">
        </div>
    </div>

    .p-top__blockItem { ... }
    .p-top__blockItem.is-01 { ... }
    .p-top__blockItem.is-02 { ... }
    .p-top__blockItem.is-03 { ... }
    .p-top__blockItem.is-04 { ... }

上記のように連番でModifierを使用する場合等、Modifierがあまり意味を持たない場合はModifierを使わずに出来るだけCSSのセレクタを使うようにします

    <div class="p-top__block">
        <div class="p-top__blockItem">
        </div>
        <div class="p-top__blockItem">
        </div>
        <div class="p-top__blockItem">
        </div>
        <div class="p-top__blockItem">
        </div>
    </div>

    .p-top__blockItem { ... }
    .p-top__blockItem:nth-child(1) { ... }
    .p-top__blockItem:nth-child(2) { ... }
    .p-top__blockItem:nth-child(3) { ... }
    .p-top__blockItem:nth-child(4) { ... }


## 最後に
ばーっと書いてみると色々直さなきゃいけない部分もある気がします。  
徐々に改善していきます。