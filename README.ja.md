# museum-theme_cb-add-on

CollectionBuilder-CSV のアドオン: Museum テーマ

このリポジトリは、CollectionBuilder-CSV（CB-CSV）のプロジェクトに、2 種類の見た目、ライト／ダークの切り替え、美術館風のトップページを足すためのテンプレートファイルを提供します。このフォルダ構成のまま、CB-CSV のリポジトリにそのままコピーして使います。

*注: そのまま使えるのは CB-CSV だけです。ほかのテンプレートには対応していません。*

[English (README.md)](README.md)

## Museum テーマ

サイト全体の見た目を 2 種類から選べます。

- **modern**: 白と紺。見出しはゴシック体。サイト名・メニュー・検索が 1 行に並ぶ
- **museum**: 生成りの紙の色。見出しは明朝体。タイトルの帯がメニューの上に出る

どちらにもダーク用の配色があります。メニューに切り替えボタンを出すこともできます（閲覧者が選ぶまでは OS の設定に従います）。
スタイルは CB-CSV が使っている Bootstrap 5.3 の [CSS 変数](https://getbootstrap.com/docs/5.3/customize/css-variables/) を通して効くので、CollectionBuilder の部品（ボタン、モーダル、表、一覧・検索ページ）は、テンプレートを変えなくても配色に従います。
フォントはページの言語で選ばれます。英語・日本語・中国語・韓国語の資料で同じテーマが使えます。
フォントのファイルやライブラリは足しません。

このアドオンに入っているもの:

| ファイル | 役割 |
| --- | --- |
| `_sass/_museum.scss` | museum の配色（`--museum-*` の変数）と部品のスタイル |
| `_sass/_modern.scss` | modern の配色、ダークの配色、1 行のヘッダー、資料ページの組み方 |
| `_includes/head/color-mode.html` | ページを描く前にライト／ダークを決める（ダークのページが一瞬白く光らないように） |
| `_includes/color-mode-toggle.html` | メニューのライト／ダーク切り替えボタン |
| `_layouts/home-museum.html` | 任意のトップページ。大きな検索欄、検索語の例、画像の一覧 |

## 使い方

CB-CSV のプロジェクトに足す:

- CollectionBuilder-CSV のプロジェクトを始める（既存のものでもかまいません）。
- このアドオンを手元に落とす（「Code」→「Download ZIP」で落とし、展開する）。
- 「README.md」「README.ja.md」「LICENSE」以外のファイルとフォルダをすべて、CB-CSV のプロジェクトにコピーする。フォルダ構成は CB-CSV と同じなので、新しいファイルが正しい場所に入ります（既存のファイルを消したり上書きしたりしないでください）。
- 下の「[既存のファイルへの変更](#既存のファイルへの変更)」の数行を書き足す。テーマはすべてのページに効くので、ほかのアドオンと違い、CB-CSV のテンプレートに数行の手入れが要ります。

### gem として入れる（別の方法）

同じファイルを Jekyll のテーマ gem にしたものが [cb-museum-theme](https://github.com/nakamura196/cb-museum-theme) です。版の番号 1 つでテーマを更新したいときは、コピーの代わりにこちらを使います。

- `Gemfile` に足す: `gem "cb-museum-theme", git: "https://github.com/nakamura196/cb-museum-theme.git", tag: "v0.1.0"`
- `_config.yml` に足す: `theme: cb-museum-theme`
- `bundle install` のあと、同じく「[既存のファイルへの変更](#既存のファイルへの変更)」を行う。

Jekyll は、gem の中のファイルより、サイトにある同じ名前のファイルを先に使います。テーマのファイルを 1 つだけ差し替えたいときは、そのファイルをサイトにコピーして直せます。

### 見た目を選ぶ

`_data/theme.yml` に足す:

```yaml
theme-style: modern     # modern / museum
color-mode-toggle: true # メニューのライト／ダーク切り替え。false でライトのまま
```

欧文のフォント（Source Serif 4 / Source Sans 3）を使うときは、同じファイルの `font-cdn` に次を書きます。

```yaml
font-cdn: '<link rel="preconnect" href="https://fonts.googleapis.com"><link rel="preconnect" href="https://fonts.gstatic.com" crossorigin><link href="https://fonts.googleapis.com/css2?family=Source+Sans+3:ital,wght@0,400..700;1,400..700&family=Source+Serif+4:wght@400..700&display=swap" rel="stylesheet">'
```

書かなければ、OS のフォントが使われます。
日本語なら、Noto Serif JP と Noto Sans JP を同じように読み込みます。

### 美術館風のトップページを使う

任意です。`pages/index.md` のレイアウトを替え、検索語の例を足します。

```yaml
---
layout: home-museum
title: Home
permalink: /
banner-featured-image: true
search-examples:
  - 地図
  - 書簡
---
```

`pages/index.md` の本文は、検索欄の横に出ます。
設定できる項目は「_layouts/home-museum.html」の冒頭のコメントにあります（例: `grid-items` に objectid を並べると、全件の代わりにその資料だけを一覧に出します）。

## 既存のファイルへの変更

テーマを CB-CSV につなぐための手入れです。どれも既存のテンプレートに書き足すだけなので、テーマを外せば元どおりに動きます。

1. `_sass/_custom.scss` の末尾に:

   ```scss
   @use "museum";
   @use "modern";
   ```

2. `_includes/head/head.html` の `<meta charset="utf-8">` のすぐ下の行に:

   ```liquid
   {% if site.data.theme.color-mode-toggle %}{% include head/color-mode.html %}{% endif %}
   ```

3. `_layouts/default.html`: `<html>` に見た目の名前を付け、modern ではメニューをタイトルの帯の上に出す。

   先頭の 2 行

   ```liquid
   <!doctype html>
   <html lang="en" class="h-100">
   ```

   を次に置き換える:

   ```liquid
   {%- assign style = site.data.theme.theme-style | default: "modern" -%}
   <!doctype html>
   <html lang="en" class="h-100" data-style="{{ style }}">
   ```

   次の 2 行

   ```liquid
       {% include collection-banner.html %}
       {% include collection-nav.html %}
   ```

   を次に置き換える:

   ```liquid
       {% if style == "modern" %}{% include collection-nav.html %}
       {% include collection-banner.html %}{% else %}{% include collection-banner.html %}
       {% include collection-nav.html %}{% endif %}
   ```

4. `_includes/collection-nav.html`: modern ではメニューの帯にサイト名も入れ、右端に切り替えボタンを置く。

   a. `<nav` の直前に、見た目を選ぶ 2 行を足す。modern の帯は中身が多いので、992px ではなく 1200px より狭くなったらメニューボタンに畳みます。

   ```liquid
   {%- assign style = site.data.theme.theme-style | default: "modern" -%}
   {%- if style == "modern" %}{% assign bp = "xl" %}{% else %}{% assign bp = "lg" %}{% endif -%}
   ```

   b. `<nav ...>` の行を次の行に置き換える。畳む幅を上の値にし、切り替えボタンを使うときは固定の `data-bs-theme` を外します（外さないと、ダークでもメニューの帯だけ明るいままになります）。

   ```liquid
   <nav class="navbar navbar-expand-{{ bp }} {{ site.data.theme.navbar-background | default: 'bg-black' }}"{% unless site.data.theme.color-mode-toggle %} data-bs-theme="{{ site.data.theme.navbar-color | default: 'dark' }}"{% endunless %}>
   ```

   c. `<div class="container">` のすぐ下に、サイト名を足す:

   ```liquid
   {% if style == "modern" %}<a class="navbar-brand" href="{{ '/' | relative_url }}">{{ site.title }}</a>{% endif %}
   ```

   その下のスマートフォン用のロゴの `d-lg-none` を `d-{{ bp }}-none` に替える。

   d. `{% include nav-search-lunr.html %}` のすぐ下に、切り替えボタンを足す:

   ```liquid
   {% if site.data.theme.color-mode-toggle %}{% include color-mode-toggle.html %}{% endif %}
   ```

英語以外のサイトでは、手順 3 の `lang="en"` もその言語に替えてください（例: `lang="ja"`）。テーマはこの値でフォントを選びます。

## 自分好みに直す

上から順に試してください。

1. `_data/theme.yml` の**設定**: `theme-style`、`color-mode-toggle`、`font-cdn`。
2. `_sass/_custom.scss` の `@use` の行のあとで**変数**を上書きする。色・フォント・角の丸みはすべて `--museum-*` の変数で、一覧は `_sass/_museum.scss`（ライト）と `_sass/_modern.scss`（modern とダーク）の冒頭にあります。例:

   ```scss
   :root { --museum-accent: #2a4f7a; --museum-accent-rgb: 42, 79, 122; }
   :root[data-bs-theme="dark"] { --museum-accent: #7da8d6; }
   :root { --museum-heading-font: var(--museum-sans); } // museum の見出しをゴシック体に
   ```

3. `_sass/_custom.scss` の変数のあとに、**規則を足す**。
4. アドオンのファイルを**直接直す**。何を直したかは書き留めておいてください。アドオンの新しい版は、手元の直しと自動では混ざりません。

## 本体を触らずにテーマを作る

このアドオンは、CollectionBuilder のテンプレートをそのまま残して CB-CSV のサイトの見た目を変える例でもあります。自分のテーマも、同じ手順で作れます。

1. **Bootstrap の変数から始める。** Bootstrap 5.3 は、色・フォント・角の丸みを CSS 変数（`--bs-body-bg`、`--bs-primary`、`--bs-border-radius` など）から読みます。新しい Sass のファイルで一度設定すれば、すべての部品が従います。`_sass/_museum.scss` は、自前の短い変数の一覧からこれらを設定しているので、1 つの配色でサイト全体が決まります。
2. **スタイルは `_sass/` の下の新しいファイルに書き**、CB-CSV がこのために空けている `_sass/_custom.scss` の末尾で `@use` する。`_base.scss` など CB-CSV の Sass のファイルは直さない。
3. **見た目の切り替えは属性に掛ける。** 2 つ目の見た目は `:root[data-style="modern"]` のようなセレクタの下に書き、その属性を `_data/theme.yml` から付ける（「既存のファイルへの変更」の手順 3）。ダークは Bootstrap の `[data-bs-theme="dark"]` を使う。
4. **HTML を足すときは、新しい include とレイアウトにする。** 名前は CB-CSV が使っていないものにする。新しいトップページは、`home-museum` のように、ページが front matter で選ぶ新しいレイアウトにする。
5. **CB-CSV のテンプレートへの手入れは、自分の include を呼ぶ 1 行にとどめ**、`theme.yml` の設定の `if` で囲む。手入れは上のように README に書き出しておく。CB-CSV の新しい版でもやり直せるように。

## 使用例

- [cb-theme-test](https://nakamura196.github.io/cb-theme-test/)（[リポジトリ](https://github.com/nakamura196/cb-theme-test)）: CB-CSV のデモ資料にこのアドオンを入れたもの。modern の見た目で、ライト／ダークの切り替えあり。

## ライセンス

コード: MIT（[LICENSE](LICENSE)）。
文書（この README と README.md）: [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/)。
