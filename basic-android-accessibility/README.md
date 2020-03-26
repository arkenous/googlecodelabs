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

