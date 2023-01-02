# APCAコントラスト日本語フォント検証

WCAG 3.0にて 検討が進められているテキストの視覚的コントラスト基準である APCAについて、日本語フォントに適応した場合の検討を行うためのツールを提供しています。

ウェブサイト：[https://apca-ja.studio-ito.net/](https://apca-ja.studio-ito.net/)

## 検証結果のアンケートについて

ツールを利用いただいた感想や、ご意見などを収集する簡単なアンケートを検討しています。
Googleフォームを利用しようかなと考えていますが、アンケート項目の検討にもご意見いただけると嬉しいです。

なるべく回答に手間がなく、少ない設問で有効なアンケートにしたいと考えていますが、なかなか難しい。

## ライセンス

本シミュレーターのAPCAコントラスト値の計算は、APCA™ for W3C & WCAG3の提供するNode moduleを利用しています。  
利用制限などがありますので、詳細についてはAPCA™ for W3C & WCAG3のライセンスを参照してください。  
[APCA™ for W3C & WCAG3 LICENSE](https://github.com/Myndex/apca-w3/blob/master/LICENSE.md)

日本語フォントはGoogle Fontsを利用しています。
個々のフォントのライセンスは、Open Font Licenseで提供されています。

## バグ報告やご意見、開発への貢献等について

当プロジェクトのイシューに登録いただくか作者のTwitterアカウントまでご連絡ください。

## 開発環境

開発環境（developブランチ）： https://develop.apca-calculator-ja.pages.dev/

開発フレームワークはAstroです。初のAstro+Svelteのため色々お作法がおかしいかもしれません。求む有識者。  
以下オリジナルのAstroについてたReadme部分。

## 🚀 Astro Project Structure

Inside of your Astro project, you'll see the following folders and files:

```code
/
├── public/
│   └── favicon.svg
├── src/
│   ├── components/
│   │   └── Card.astro
│   ├── layouts/
│   │   └── Layout.astro
│   └── pages/
│       └── index.astro
└── package.json
```

Astro looks for `.astro` or `.md` files in the `src/pages/` directory. Each page is exposed as a route based on its file name.

There's nothing special about `src/components/`, but that's where we like to put any Astro/React/Vue/Svelte/Preact components.

Any static assets, like images, can be placed in the `public/` directory.

## 🧞 Commands

All commands are run from the root of the project, from a terminal:

| Command                | Action                                             |
| :--------------------- | :------------------------------------------------- |
| `npm install`          | Installs dependencies                              |
| `npm run dev`          | Starts local dev server at `localhost:3000`        |
| `npm run build`        | Build your production site to `./dist/`            |
| `npm run preview`      | Preview your build locally, before deploying       |
| `npm run astro ...`    | Run CLI commands like `astro add`, `astro preview` |
| `npm run astro --help` | Get help using the Astro CLI                       |

## 👀 Want to learn more?

Feel free to check [our documentation](https://docs.astro.build) or jump into our [Discord server](https://astro.build/chat).
