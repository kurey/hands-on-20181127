# Angular入門

## Contents

- [Step1: 準備](#step1)
- [Step2: プロジェクトを作ろう](#step2)
- [Step3: コンポーネントを作ろう](#step3)
- [Step4: コンポーネント間でデータをやり取りしよう](#step4)
- [Step5: ルーティングを設定しよう](#step5)
- [Step6: AWS IAMでユーザを払い出そう](#step6)
- [Step7: AWS Rekognitionを使って画像認識をしてみよう](#step7)

## Step1

### AWS CLIをインストール

- Windowsの方
  - [AWS CLI(64bit)](https://s3.amazonaws.com/aws-cli/AWSCLI64.msi)をインストール
    - 32bitの方は[こちら](https://s3.amazonaws.com/aws-cli/AWSCLI32.msi)
  - `aws help`でヘルプが表示されればOK!

- Macの方
  - [Homebrew](https://brew.sh/index_ja)をインストール
  - `brew install awscli`
  - `aws help`でヘルプが表示されればOK!

### Node.jsをインストール

- Windowsの方
  - [NodistSetup](https://github.com/nullivex/nodist/releases)をインストール
  - `nodist + 8.9.0`
  - `node -v`
  - v8.9.0と表示されればOK!
- Macの方
  - `touch ~/.bash_profile`
  - `curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash`
  - `nvm install v8.9.0`
  - `node -v`
  - v8.9.0と表示されればOK!

### Angular CLIをインストール

- `npm install -g @angular/cli`
- `ng version`
- バージョンが表示されればOK!

## Step2

### Angular CLIでプロジェクトの作成

- `ng new [プロジェクト名]`

  上記コマンド実行すると、以下2点の入力を求められます。

  - `? Would you like to add Angular routing? [y/N]`
    - 訳：ルーティング追加する？
    - `y`を選択

  - `? Which stylesheet format would you like to use?`
    - 訳：スタイルシートはどれ使う？
    - `SCSS`を選択

### 作ったプロジェクトをローカルで動かしてみよう

- `cd [プロジェクト名]`

- `ng serve --open`
  - 起動が完了するとブラウザが立ち上がります。

## Step3

### コンポーネントを作って表示してみよう

- `ng g component frameworks`
  - `app/frameworks`が作られます。

- `app.component.html`を以下のように書き換えて保存してください。

```html
<app-frameworks></app-frameworks>
```

- ブラウザを確認すると画面上に `frameworks works!`と表示されているはずです。
  - これは `app/frameworks/frameworks.component.html`の内容が投影されています。

### TypeScriptとHTMLの連携をしてみよう

- `app/frameworks/frameworks.component.ts`を開いて、以下のコードを追加してください。

```typescript
import { Component, OnInit } from '@angular/core';

@Component({
  /** コンポーネントのカスタムタグ名 */
  selector: 'app-frameworks',
  /** 投影するHTMLの設定 */
  templateUrl: './frameworks.component.html',
  /** 利用するSCSSの設定 */
  styleUrls: ['./frameworks.component.scss']
})
export class FrameworksComponent implements OnInit {

  /** タイトル */
  title: string = 'Hello Angular!';

  /** フレームワークリスト */
  frameworks: Array<string> = [
    'Angular',
    'React',
    'Vue',
  ];

  /** 選択したフレームワーク */
  selectedFramework: string;

  constructor() { }

  ngOnInit() {
  }

  /**
   * クリック処理
   * @param framework 選択したフレームワーク
   */
  onClick(framework: string) {
    this.selectedFramework = framework;
  }

  /**
   * 選択したフレームワークに対しての正解/不正解の結果を返却
   */
  correct(): string {
    switch (this.selectedFramework) {
      case 'Angular':
        return '大正解！';
      case 'React':
      case 'Vue':
        return '残念！今回利用しているフレームワークはAngularです！';
    }
  }

}
```

- `app/frameworks/frameworks.component.html`を開いて、以下のように編集してください。

```html
<h1>{{ title }}</h1>

<p>今回のハンズオンで利用しているフレームワークは？</p>

<div *ngFor='let framework of frameworks'>
  <button (click)='onClick(framework)'>{{ framework }}</button>
</div>

<p *ngIf='selectedFramework'>
  {{ correct() }}
</p>
```

- ポイント！

  - 変数の値をHTML上にするには`{{  }}`を使って表示することができます。

  - `*ngFor='let [一時変数名] of [配列変数名]'`を使うことで配列をループさせて表示させることができます。

  - `*ngIf='[条件文]'`を使うことで表示/非表示の切り替えを行うことができます。

  - 他にもAngularではDOMの見た目や動作を変更する記法がたくさんありますので、興味があれば調べてみてください！
    - これを`Directive`(ディレクティブ)と呼びます。

## Step4

### 用語

このステップではコンポーネント間のデータ送受信を実践します。

そこで呼び出し元のコンポーネントを`親コンポーネント`、呼び出し先のコンポーネントを`子コンポーネント`と呼びます。

今回は先程作成した`frameworks.component`が親コンポーネント。これから作る`select-framework.component`が子コンポーネントとなります。

### コンポーネント間でデータの受け渡しをするモデルを作成しましょう

- `framework.model.ts`を`app/frameworks`フォルダの下に作成してください。

```typescript
export interface Framework {
    id: FrameworkId;
    name: FrameworkName;
}

export enum FrameworkId {
    ANGULAR = 1,
    REACT,
    VUE
}

export enum FrameworkName {
    ANGULAR = 'Angular',
    REACT = 'React',
    VUE = 'Vue'
}

export class Angular implements Framework {
    id = FrameworkId.ANGULAR;
    name = FrameworkName.ANGULAR;
}

export class React implements Framework {
    id = FrameworkId.REACT;
    name = FrameworkName.REACT;
}

export class Vue implements Framework {
    id = FrameworkId.VUE;
    name = FrameworkName.VUE;
}
```

- ポイント！
  - コンポーネント間でデータのやり取りをする際はモデルを使ってインターフェイス定義を使うと見通しがよくなるよ。

### フレームワークの選択する部分を子コンポーネントとして作成

- `ng g component frameworks/select-framework`

- `app/frameworks/select-framework.component.ts`を以下のように編集

```typescript
import { Component, OnInit, Input, Output, EventEmitter } from '@angular/core';
import { Framework } from '../framework.model';

@Component({
  selector: 'app-select-framework',
  templateUrl: './select-framework.component.html',
  styleUrls: ['./select-framework.component.scss']
})
export class SelectFrameworkComponent implements OnInit {

  /** フレームワークのリスト */
  @Input() frameworks: Array<Framework>;

  /** 選択時のイベント */
  @Output() selected = new EventEmitter<Framework>();

  constructor() { }

  ngOnInit() {
  }

  /**
   * クリック処理
   * @param framework 選択したフレームワーク
   */
  onClick(framework: Framework) {
    this.selected.emit(framework);
  }
}
```

- ポイント！
  - 呼び出し側からデータを受け取るプロパティを`@Input()`で定義します。
  - 呼び出し元にデータを受け渡すプロパティを`@Output()`で定義します。

- `app/frameworks/select-framework.component.html`を以下のように編集

```html
<div *ngFor='let framework of frameworks'>
  <button (click)='onClick(framework)'>{{ framework.name }}</button>
</div>
```

### 親コンポーネント側で子コンポーネントの呼び出し

- `app/frameworks/framework.component.ts`を以下のように編集

```typescript
import { Component, OnInit } from '@angular/core';
import { Angular, React, Vue, Framework, FrameworkId } from './framework.model';

@Component({
  selector: 'app-frameworks',
  templateUrl: './frameworks.component.html',
  styleUrls: ['./frameworks.component.scss']
})
export class FrameworksComponent implements OnInit {

  /** タイトル */
  title: string = 'Hello Angular!';

  /** フレームワークリスト */
  frameworks: Array<Framework> = [
    new Angular(),
    new React(),
    new Vue()
  ];

  /** 選択したフレームワーク */
  selectedFramework: Framework;

  constructor() { }

  ngOnInit() {
  }

  /**
   * 選択したフレームワークに対しての正解/不正解の結果を返却
   */
  correct(): string {
    switch (this.selectedFramework.id) {
      case FrameworkId.ANGULAR:
        return '大正解！';
      case FrameworkId.REACT:
      case FrameworkId.VUE:
        return '残念！今回利用しているフレームワークはAngularです！';
    }
  }

}
```

- `app/frameworks/framework.component.html`を以下のように編集

```html
<h1>{{ title }}</h1>

<p>今回のハンズオンで利用しているフレームワークは？</p>

<app-select-framework
    [frameworks]='frameworks'
    (selected)='selectedFramework=$event' >
</app-select-framework>

<p *ngIf='selectedFramework'>
  {{ correct() }}
</p>
```

- ポイント！
  - 子コンポーネント側で定義した`@Input()`にデータを渡す場合は`[子コンポーネントの変数名]='親コンポーネントの変数名'`
  - 子コンポーネント側で定義した`@Output()`からデータを受け取る場合は`(子コンポーネントの変数名)='受け取る処理(関数可)'`
    - `$event`というのが子コンポーネント側で`emit()`が呼び出された時の引数で渡されたデータとなります。（今回の場合はFramework）
    - 今回の場合は、親コンポーネントの`selectedFramework`変数に代入するだけだったため、`selectedFramework=$event`という代入式をHTML上に直接書いています。（本当は関数呼び出しをしたほうがいいです汗）

- 動き自体は変わりませんが、コンポーネントが二つになったことで再利用性が高まったり、コンポーネント毎の責任範囲が小さくなることで拡張性・保守性が良くなりました！

## Step5

### 2つの画面をルーティングしてみよう

- 二つのコンポーネントを用意します。

  - `ng g component first`

  - `ng g component second`

- `app/app-routing.module.ts`を以下のように編集します。

```typescript
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { FirstComponent } from './first/first.component';
import { SecondComponent } from './second/second.component';

const routes: Routes = [
  {
    /** URLパス */
    path: 'first',
    /** リンクさせるコンポーネント */
    component: FirstComponent
  },
  {
    path: 'second',
    component: SecondComponent
  },
  /** リダイレクト */
  {
    path: '**',
    redirectTo: 'first'
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

- `app/app.component.html`を以下のように編集

```html
<button [routerLink]="'/first'">first component表示</button>
<button [routerLink]="'/second'">second component表示</button>
<router-outlet></router-outlet>
```

- ポイント！

  - SPAの特徴として、画面遷移する度にブラウザ自体の読み込みは発生せず、Angularが裏でDOMの書き換えを動的に行なっています。
  - `[routerLink]`というのがコンポーネントを切り替え(画面遷移)をさせるAngularに用意されているディレクティブの1つとなります。HTMLで例えると`<a>`タグの`href`みたいなものですね。
  - `<router-outlet>`と書かれた部分が`app/app-routing.module.ts`に記述したパスとコンポーネントに紐づけてコンポーネントの切り替えを実現しています。
  - また、画面遷移する際にパラメータを渡したい時もあるでしょう。その際は`[queryParams]="{key: value}"`という形で遷移先のコンポーネントに値を受け渡すことも可能です。


さて、ここまででAngularの入門編とさせていただきます。

ここまでで自分で画面を追加、画面間の連携、画面遷移までができるようになりましたので、簡単なサイトであればAngularで作れるようになったはずです！（？）


## Step6

- AWSの`IAM`を使って今回利用する`Rekognition`に対して最低限の操作ができるユーザを作りましょう。

- ブラウザから[マネジメントコンソール](https://ap-northeast-1.console.aws.amazon.com/console/home?region=ap-northeast-1)を開き、メールアドレスとパスワードを入力し、ログインしましょう

  ![ログイン](./ログイン.png)

- 検索フォームに`IAM`と入力し、`IAM`を選択します

  ![マネジメントコンソール](./マネジメントコンソール.png)

- メニューから`ユーザー`を選択します

  ![IAM-step1](./IAM-step1.png)

- `ユーザーを追加`を選択します

  ![IAM-step2](./IAM-step2.png)

- `ユーザ名`を入力し、`アクセスの種類`の`プログラムによるアクセス`のみチェックし、次のステップへ

  ![IAM-step3](./IAM-step3.png)

- `既存ポリシーを直接アタッチ`を選択し、検索フォームで`rekognition`を検索
  - `AmazonRekognitionReadOnlyAccess`のみ選択し、次のステップへ
  - 検索すると3つHitしますので、`説明`のところに`Access to all Read rekognition APIs`と書いてあるやつを選択

  ![IAM-step4](./IAM-step4.png)

- 何も入力せず、次のステップへ

  ![IAM-step5](./IAM-step5.png)

- 作成される内容を確認し、問題なければ`ユーザーの作成`を選択
  - 不安だと思う方は周りのスタッフに声をかけて確認してもらってください！

  ![IAM-step6](./IAM-step6.png)

- `.csvのダウンロード`から認証情報をダウンロードしてください

  ![IAM-step7](./IAM-step7.png)

- ここまででIAMユーザの作成は完了です


- ポイント！

  - IAMはAWSで提供された様々なサービスに対して「誰が」「どのサービス」に「何ができるのか」の細かな定義を行うことができるサービスです
    - よくブログなどで目にしませんか？「アカウント不正アクセスされてAWSの利用明細がすごい額になっていた…」という事例を。
    - 今回作成したユーザは`Rekognition`に対して`読取専用`の権限だけ付与しましたが、不要になったユーザはできる限り削除することをオススメします
    - Rekognitionの料金は以下の通り(2018年11月22日時点)
      ![rekognition-price1](./rekognition-price1.png)
      ![rekognition-price2](./rekognition-price2.png)

## Step7

- 以下のプロジェクトをgit cloneして下さい。
  - `git clone https://github.com/kurey/aws-rekognition-sample.git`
  - `npm install`
    - ちなみに、AWS SDKのバージョンは`2.351.0`でないとエラーとなるため、あえて最新を指定していません！

- 以下のファイルを編集して下さい。
  - `src/app/services/aws/aws.service.ts`(initializeAWS関数)
  - `アクセスキー`(28行目)、`シークレットキー`(29行目)を[Step6](#Step6)で各自で作成したIAMユーザのアクセスキーとシークレットキーに変更して下さい。

```typescript
　〜省略〜
  /**
   * AWSの設定
   */
  initializeAWS() {
    /**
     * ！！注意！！
     * アクセスキー、シークレットキーは本来JSに直接書くのは大変危険です。
     * Rekognitionを利用する場合、サーバサイド側で連携するか
     * CognitoのUserPoolsを利用して認証を行うのがベターです。
     * 
     * くれぐれもJS側でアクセスキー、シークレットキーを管理しない様に注意しましょう！
     */
    AWS.config.update({
→     accessKeyId: 'アクセスキー',
→     secretAccessKey: 'シークレットキー',
      region: 'ap-northeast-1',
    });

    // RekognitionのAPIバージョン
    AWS.config.apiVersions = {
      rekognition: '2016-06-27'
    };
  }
　〜省略〜
```

- `ng serve --open`
  - `http://localhost:4200/detect/labels`
  - ブラウザ上で画像を選択すると、画像認識の結果が表示されます。
    ![認識結果](./認識結果.png)

## 余った人向け

AWS Rekognitionには色々な画像認識が可能です。更には動画分析もできます。

- 時間が余った人はオンライン上でAWS Rekognitionの[デモ](https://ap-northeast-1.console.aws.amazon.com/rekognition/home?region=ap-northeast-1#/face-detection)を試すことができますので、色々遊んでみましょう！

- もしくは、今回利用したサンプルアプリをもとにAPIリクエストを変更してみるのもいいでしょう
  - `app/src/services/aws/aws.service.ts`にテキスト検出のリクエストが行える関数も用意してあります(動作確認してないですが笑)