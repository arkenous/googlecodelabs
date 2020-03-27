# Basic Android Accessibility

[https://codelabs.developers.google.com/codelabs/basic-android-accessibility/](https://codelabs.developers.google.com/codelabs/basic-android-accessibility/#0)

## Introduction

アクセシビリティに配慮してアプリを実装することは、全てのユーザに対してプラスとなる。
  - Gmailにキーボードショートカットがあることで、パワーユーザがより快適に作業できる
  - ハイコントラストであることで、照り付けの眩しい環境でも画面を視認しやすい
  - 音声操作が可能であることで、料理中でもデバイスを操作できる

このCodelabにより、何らかの障碍を持たれている方がAndroidアプリをどう利用しているかの知見を得られ、これらのユーザに対してより良い体験を得ていただくためのアプリの実装方法を学べる。

## Enabling and Using TalkBack

TalkBack：Androidで用意されたスクリーンリーダ。Pixel端末でこれを有効にするには、以下の操作を行う。

Settings（設定） -> Accessibility（ユーザー補助） -> Screen readers（スクリーンリーダー）の下にあるTalkBack -> サービスの使用 をONに

読み上げ音声をスクリーン上に表示する場合は、以下の操作を行う。

TalkBack -> Settings（設定） -> Developer settings（デベロッパー向けの設定） -> Display speech output（音声出力の表示） をONに

画面上のコンポーネントをシングルタップすると、タップしたコンポーネントが緑色の四角で囲われ（フォーカスが当たる）読み上げられる。この状態で、画面上のどこでもいいのでダブルタップすると、フォーカスが当たったコンポーネントが選択操作される。

左右方向にスワイプすることで、画面上のコンポーネントを順繰りにフォーカスできる。
[TalkBackには他にも様々なジェスチャ操作が用意されている](https://support.google.com/accessibility/android/answer/6151827)。

## Content Labeling

十分な意味を持ち、有用で、説明的なラベルである限りにおいて、全ての操作可能な要素をTalkBackは読み上げてくれる。そのようなラベルが欠けていれば、その要素が持つ機能を適切に読み上げられない。またいくつかの要素の読み上げをスキップすることもある。

EditTextに対しては、contentDescriptionではなくhintによって読み上げ対応するのが望ましい。

ボタンに文字列が設定されている場合、TalkBackはその文字列を読み上げてくれる。
文字列が設定されていないようなボタンであれば、「ラベルなし、ボタン」とだけ読み上げられる。
トグルで動作するボタンの場合、トグルの状態まで踏まえたラベリングをしないと、ユーザは現在の状態を把握できない。

ユーザが操作をしない、飾りのViewに対しては、読み上げの必要がないので`contentDescription="@null"`を設定する。

操作によって動的にアセットを切り替えているようなViewの場合、レイアウトXMLでは`contentDescription="@null"`にし、切り替え処理をしているコードにおいて、`setContentDescription(CharSequence)`で動的に読み上げテキストも切り替える。

ImageViewのような要素をキーボードから選択できるようにしたい場合、`android:focusable="true"`を設定しないといけない場合がある。

ラベリングする際は、以下の各点を押さえる。
- 簡潔に
  - 特に、点字を利用されているユーザの場合、一行あたり40文字未満しか表示できないため
- 物理的な操作方法を示す文言（クリック、タップ、押下）を含めない
  - AccessibilityServiceがこの辺りの面倒を見てくれるので。Switch AccessやVoice AccessといったTalkBack以外の利用者は、スクリーンを物理的にタッチしない
- 文脈的に最も重要な情報を、ラベルの頭に持ってくること
  - ラベルをいちいち全て再生させる必要がなくなる

## Grouping Content

Viewの並びに沿ってTalkBackされないことがある。
また、要素が適切にグルーピングされていない場合、表示される要素が多いと、順にそれらをなぞっていくしか方法がなく、操作性が悪くなる。
複数の曲情報が表示されるような画面で、曲ごとのグルーピングができていないと、各曲の曲名やアーティストなどを一つずつなぞらないといけなくなる。曲名だけで探す、みたいなことができない。

TextViewのようなフォーカスが有効でない要素をグルーピングする場合、フォーカスが有効なコンテナでこれらを囲うことで、これら要素を一塊で一気に読み上げてくれる。
読み上げの塊を細分化したい場合は、グループごとに`android:focusable="true"`を付与したLinearLayoutやRelativeLayoutといったViewGroupで囲えば良い。

グルーピングや要素の順序立てで押さえるべきポイントは以下の三つ。
- View階層の順序と、スクリーン上で見えるグルーピングと、音声フィードバックで読み上げる順序を考慮する
- non-focusableな複数の要素を一つの要素として読ませたいなら、focusableなコンテナでまとめるべきである
- 関連する要素を論理的にまとめようとすると、ViewGroupを入れ子にせざるを得ないときがある

## Using a Live Region

今までの例ではユーザが明示的にフォーカスを当てたものをTalkBackで読み上げさせていた。
ある操作をトリガーにして動的に更新される要素がある場合、いちいちその要素まで移動しフォーカスを当てずとも自動的に読み上げてほしい場合がある。
これを実現するためには、TalkBackのLive Regionを用いる。動的な変更を自動的に読み上げてほしいViewに対し、`android:accessibilityLiveRegion`をセットすると良い。セットしたViewが更新されると、まずこれが読み上げられ、その後フォーカスされているViewが読み上げられる。

`accessibilityLiveRegion`で指定できる値のうち、`polite`を用いた場合、他のViewの読み上げ中にLive Regionを指定したViewが更新されても、読み上げ処理に割り込まない。`assertive`を用いた場合は、割り込む挙動となる。

Live Regionの使用はなるべく控えるべきであり、`assertive`を用いるのは避けるべきである。

Viewが頻繁に更新されるような場合、Live Regionを用いてしまうとユーザに混乱を生じさせアプリが使い物にならなくなり、イライラさせてしまう。Live Regionは容易に使いすぎてしまう、特にアクセシビリティを始めたてであればなおさら。Live Regionは特例であり、当たり前のことではない。