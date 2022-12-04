# Live Demo

Here are several examples of basic masks that you could create with **Maska**.
This demo is interactive: feel free to edit the examples.

<div id="demo-app"></div>
<script src="dist/demo.js"></script>

# Install

<!-- tabs:start -->
## **Via npm**

```
npm i maska
```

## **From CDN**

To include library from CDN, use UMD format and prefix all classes and directives with `Maska.`

``` html
<script src="https://cdn.jsdelivr.net/npm/maska@latest/dist/maska.umd.js"></script>
<script>
  new Maska.MaskInput("[data-maska]") // for masked input
  const mask = new Maska.Mask({ mask: "#-#" }) // for programmatic use
</script>
```
<!-- tabs:end -->

# Usage

**Maska** library consists of three main components:

- `Mask` class to mask string values
- `MaskInput` class to apply `Mask` processing to `<input>`
- `vMaska` directive to simplify using of library within Vue components

<!-- tabs:start -->
## **Vanilla JS**

Start with simple input element and `data-maska` attribute:

``` html
<input data-maska="#-#">
```

Then import and init `MaskInput`, passing input element(s) or `querySelector` expression as first argument:

``` js
import { MaskInput } from "maska"
new MaskInput("[data-maska]")
```

Usually you set mask via `data-maska` attribute. But you can pass mask and other [options](#options) via second argument (it will be a default options that can be overriden by `data-maska-` attributes):

``` js
new MaskInput("input", { mask: "#-#" })
```

To destroy mask use `destroy()` method:

``` js
const mask = new MaskInput(...)
mask.destroy()
```

## **Vue**

Import `vMaska` directive and apply it to the input along with `data-maska` attribite:

<!-- tabs:start -->
### **Composition API**

``` html
<script setup>
import { vMaska } from "maska"
</script>

<template>
  <input v-maska data-maska="#-#">
</template>
```

### **Options API**

``` html
<script>
import { vMaska } from "maska"

export default {
  directives: { maska: vMaska }
}
</script>

<template>
  <input v-maska data-maska="#-#">
</template>
```
<!-- tabs:end -->

### Set options with directive

To set default options for the mask you could use directive *argument* (part after `v-maska:`). It can be plain or reactive object and should be wrapped in `[]`:

<!-- tabs:start -->
### **Composition API**

``` html
<script setup>
import { reactive } from "vue"
import { vMaska } from "maska"

const options = reactive({
  mask: "#-#",
  eager: true
})
</script>

<template>
  <input v-maska:[options]>
</template>
```

### **Options API**

``` html
<script>
import { vMaska } from "maska"

export default {
  directives: { maska: vMaska },
  data: () => ({
    options: {
      mask: "#-#",
      eager: true
    }
  })
}
</script>

<template>
  <input v-maska:[options]>
</template>
```
<!-- tabs:end -->

### Bind to variable

It’s very easy to bind mask result to a variable.
This variable should be reactive object and will contains three fields:

- `masked`: string with masked result
- `unmasked`: string with unmasked result
- `completed`: boolean flag indicating that mask is completed

<!-- tabs:start -->
### **Composition API**

``` html
<script setup>
import { reactive } from "vue"
import { vMaska } from "maska"

const binded = reactive({})
</script>

<template>
  <input v-maska="binded">
  <p v-if="binded.completed">
    Masked value: {{ binded.masked }}
    Unmasked value: {{ binded.unmasked }}
  </p>
</template>
```

### **Options API**

``` html
<script>
import { vMaska } from "maska"

export default {
  directives: { maska: vMaska },
  data: () => ({
    binded: {
      masked: "",
      unmasked: "",
      completed: false
    }
  })
}
</script>

<template>
  <input v-maska="binded">
  <p v-if="binded.completed">
    Masked value: {{ binded.masked }}
    Unmasked value: {{ binded.unmasked }}
  </p>
</template>
```
<!-- tabs:end -->

#### Global registration of directive

<!-- tabs:start -->
### **Vue 3**

``` js
import { createApp } from "vue"
import { vMaska } from "maska"

createApp({}).directive("maska", vMaska)

// or in case of CDN load
Vue.createApp({}).directive("maska", Maska.vMaska)
```

### **Vue 2**

``` js
import Vue from "vue"
import { vMaska } from "maska"

Vue.directive("maska", vMaska)

// or in case of CDN load
Vue.directive("maska", Maska.vMaska)
```
<!-- tabs:end -->
<!-- tabs:end -->

# Options

## `Mask` options

Every option of `Mask` class can be set via `data-maska-` attributes or by passing on init.
Options passed on init will be used as defaults and could be overriden by `data-maska-` attributes.

<!-- tabs:start -->
### **Description**

- `mask` / `data-maska` — mask to apply (**string**, **array of strings** or **function**)
- `tokens` / `data-maska-tokens` — custom tokens object
- `tokensReplace` / `data-maska-tokens-replace` — if custom tokens should replace default tokens (if not set they will merge)
- `eager` / `data-maska-eager` — eager mode
- `reversed` / `data-maska-reversed` — reversed mode

### **Types**
``` js
interface MaskOptions {
  mask?: MaskType
  tokens?: MaskTokens
  tokensReplace?: boolean
  eager?: boolean
  reversed?: boolean
}
```
<!-- tabs:end -->

``` html
<input data-maska="A-A" data-maska-tokens="A:[A-Z]" data-maska-eager>
```

## `MaskInput` options

`MaskInput` options could be set only by passing second argument on init.
Along with `MaskInput` options you could pass any `Mask` options as well (or set them via `data-maska-` attributes).

<!-- tabs:start -->
### **Description**

- `onMaska` — [callback](#events) on every mask processing
- `preProcess` — [hook](#hooks) before mask processing
- `postProcess` — [hook](#hooks) after mask processing

### **Types**
``` js
interface MaskInputOptions extends MaskOptions {
  onMaska?: (detail: MaskaDetail) => void
  preProcess?: (value: string) => string
  postProcess?: (value: string) => string
}
```
<!-- tabs:end -->

``` js
new MaskInput("input", {
  mask: "#-#",
  reversed: true,
  onMaska: (detail) => console.log(detail.completed)
})
```

# Tokens

There are 3 tokens defined by default:

``` js
{
  '#': { pattern: /[0-9]/ },       // digits
  '@': { pattern: /[a-zA-Z]/ },    // letters
  '*': { pattern: /[a-zA-Z0-9]/ }, // letters & digits
}
```

?> Use `!` before token to escape symbol. For example `!#` will render `#` instead of a digit.

## Custom tokens

Add custom tokens via `data-maska-tokens` attribute or by `tokens` option:

<!-- tabs:start -->
### **By data-attr**

When using `data-maska-tokens`, there are two possible formats:

- **Full form** should be a valid JSON-string (but can use both single and double quotes) with pattern in string format without delimiters
- **Simple form** should be a string in format: `T:P:M|T:P:M` where:
  - `T` is token
  - `P` is pattern in string form
  - `M` is optional modifier (see below)
  - `|` is separator for multiple tokens

``` html
<input data-maska="Z-Z" data-maska-tokens="{ 'Z': { 'pattern': '[A-Z]' }}">
<input data-maska="Z-Z" data-maska-tokens="Z:[A-Z]">
<input data-maska="Z-z" data-maska-tokens="Z:[A-Z]|z:[a-z]:multiple">
```

?> You can’t set `transform` function for token via `data-maska-tokens`.
If you need this, use `tokens` option instead.

### **By option**

When using `tokens` option, pattern should be a valid regular expression object:

``` js
new MaskInput("[data-maska]", {
  mask: "A-A",
  tokens: {
    A: { pattern: /[A-Z]/, transform: (chr) => chr.toUpperCase() }
  }
})
```
<!-- tabs:end -->

## Token modifiers

Every token can have a modifier, for example:

``` js
{
  0: { pattern: /[0-9]/, optional: true },
  9: { pattern: /[0-9]/, repeated: true },
}
```

- `optional` for optional token
- `multiple` for token that can match multiple characters till the next token starts
- `repeated` for tokens that should be repeated. This token will match only one character, but the token itself (or group of such tokens) can be repeated any number of times

| Modifier   | Example usage    | Mask                               | Tokens
| ---        | ---              | ---                                | ---
| `optional` | IP address       | `#00.#00.#00.#00`                  | `0:[0-9]:optional`
| `multiple` | CARDHOLDER NAME  | `A A`                              | `A:[A-Z]:multiple`
| `repeated` | Money (1 234,56) | `9 99#,##` <small>reversed</small> | `9:[0-9]:repeated`

## Transform tokens

For custom tokens you can define `transform` function, applied to a character before masking.
For example, transforming letters to uppercase on input:

``` js
{
  A: { pattern: /[A-Z]/, transform: (chr) => chr.toUpperCase() }
}
```

?> You can also use [hooks](#hooks) for transforming whole value before/after masking.

# Dynamic masks

Pass `mask` as **array** or **function** to make it dynamic:

- With **array** a suitable mask will be chosen by length (smallest first)
- With **function** you can select mask with custom logic using value

``` js
new MaskInput("input", {
  mask: (value) => value.startsWith('1') ? '#-#' : '##-##'
})
```

# Hooks

Use hooks for transforming whole value:

- `preProcess` hook called before mask processing
- `postProcess` hook called after mask processing, but before setting input's value

For example, you can use it to check for a maximum length of masked string:

``` js
new MaskInput("input", {
  postProcess: (value) => value.slice(0, 5)
})
```

# Events

You can subscribe to `maska` event, fired every time when mask applied:

<!-- tabs:start -->
## **Vanilla JS**

``` js
document.querySelector("input").addEventListener("maska", onMaska)
```

## **Vue**

``` html
<input v-maska data-maska="#-#" @maska="onMaska" />
```
<!-- tabs:end -->

Callback will receive custom event contains `detail` property with given structure:

<!-- tabs:start -->
### **Description**

- `masked`: masked value
- `unmasked`: unmasked value
- `completed`: flag that current mask is completed

### **Types**
``` js
interface MaskaDetail {
  masked: string
  unmasked: string
  completed: boolean
}
```
<!-- tabs:end -->

``` js
const onMaska = (event) => {
  console.log({
    masked: event.detail.masked,
    unmasked: event.detail.unmasked,
    completed: event.detail.completed
  })
}
```

Alternatively, you can pass callback function directly with `MaskInput` option `onMaska`:

<!-- tabs:start -->
### **Vanilla JS**
``` js
new MaskInput("input", {
  onMaska: (detail) => console.log(detail.completed)
})
```

### **Vue**
``` html
<script setup>
const options = {
  onMaska: (detail) => console.log(detail.completed)
}
</script>

<template>
  <input v-maska:[options]>
</template>
```
<!-- tabs:end -->

# Programmatic use

You can use mask function programmatically by importing `Mask` class.
There are three methods available:

- `masked(value)` returns masked version of given value
- `unmasked(value)` returns unmasked version of given value
- `completed(value)` returns `true` if given value makes mask complete

``` js
import { Mask } from "maska"

const mask = new Mask({ mask: "#-#" })

mask.masked("12") // = 1-2
mask.unmasked("12") // = 12
mask.completed("12") // = true
```