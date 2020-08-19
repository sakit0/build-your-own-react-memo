# Step Zero: Review

このReactのコードを素のJSに置き換えて、Reactの動作を知る章。

```js
const element = <h1 title="foo">Hello</h1>
const container = document.getElementById("root")
ReactDOM.render(element, container)
```

## createElement
一行目について
```js
const element = <h1 title="foo">Hello</h1> 
```

JSXをcreateElementに置き換える(実際はBabelがやってる)

```js
const element = React.createElement(
  "h1",
  { title: "foo" },
  "Hello"
)
```

### React本体を見る
- createElementは引数からオブジェクトを生成する(いくつかの検証はある)
- [ReactElement.js](https://github.com/facebook/react/blob/f342a2399fe121d84cf488adc8f22805b2278370/packages/react/src/ReactElement.js#L348)がcreateElementの実体ぽい
- [React公式のcreateElement()](https://reactjs.org/docs/react-api.html#createelement)を見るとfragmentも受け付けている

なので、さきほどのcreateElementの結果をJSにするとこうなる
(ソースコード読むとほんとは`key`や`ref`なども渡すようになってたが、この章では抜いてるぽい)

```js
const element = {
  type: "h1",
  props: {
    title: "foo",
    children: "Hello",
  },
}
```

- `props`はオブジェクトになる
- `children`は文字列以外にも本来配列もあり、中身はDOMツリー

## ReactDOM.render(element, container)

ReactがDOMを変更する箇所

```js
const node = document.createElement(element.type)
node["title"] = element.props.title

// <h1 title="foo"></h1>
```

- `element.type`を使用して、DOMを作成
- `element.props`を使用して、コンテンツを追加

```js
const node = document.createElement(element.type)
node["title"] = element.props.title

// 追加
const text = document.createTextNode("")
text["nodeValue"] = element.props.children

// Hello
```

- テキストを生成し、`nodeValue`に`element.props.children`を割り当て

```js
const node = document.createElement(element.type)
node["title"] = element.props.title

const text = document.createTextNode("")
text["nodeValue"] = element.props.children

// 追加
node.appendChild(text)
container.appendChild(node)

// <h1 title="foo">Hello</h1>
```

- 最後にテキストを`node`に割り当てて、`container`に`node`を割り当てる

### React本体を見る

- [ReactDOM.render](https://github.com/facebook/react/blob/f342a2399fe121d84cf488adc8f22805b2278370/packages/react-dom/src/client/ReactDOMLegacy.js#L287)が、ReactDOMLegacy.jsというファイル名なんだが、Legacyなの?見る場所間違えてるのかな。

## 章のまとめ

React

```js
const element = <h1 title="foo">Hello</h1>
const container = document.getElementById("root")
ReactDOM.render(element, container)
```

JS
```js
const element = {
  type: "h1",
  props: {
    title: "foo",
    children: "Hello",
  },
}

const container = document.getElementById("root")

const node = document.createElement(element.type)
node["title"] = element.props.title

const text = document.createTextNode("")
text["nodeValue"] = element.props.children

node.appendChild(text)
container.appendChild(node)
```