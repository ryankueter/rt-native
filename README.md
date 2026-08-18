# rt-native - Rich Text Editor

**Author:** Ryan Kueter | **Updated:** August, 2026

**rt-native.js** HTML Editor is a free native web component that provides accessibility features and a wide variety of elements and customizations that make it one of the most robust and flexible HTML editors available. It allows the programmer to apply custom .css files to the preview window, to see how the content will be displayed in production. The editor uses embedded .svg Google Font Icons and the shadow DOM to isolate the HTML from inheriting the existing page styles. No frameworks, no build step, no dependencies — drop **one script tag** into any HTML page and you're done.

---

## Table of Contents

1. [Files](#files)
2. [Installation](#installation)
3. [Quick Start](#quick-start)
4. [HTML Attributes](#html-attributes)
   - [Example](#example)
5. [JavaScript API](#javascript-api)
   - [getValue()](#getvalue)
   - [getPlainText()](#getplaintext)
   - [setValue()](#setvalue)
   - [configure()](#configure)
   - [setReadOnly()](#setreadonly)
   - [setPreviewCssFiles()](#setpreviewcssfiles)
   - [setPreviewCssFile()](#setpreviewcssfile)
   - [setPreviewCss()](#setpreviewcss)
   - [At-rules](#at-rules)
   - [addCustomButton()](#addcustombutton)
   - [setCustomButtons()](#setcustombuttons)
   - [removeCustomButton()](#removecustombutton)
   - [clearCustomButtons()](#clearcustombuttons)
6. [Spellchecking](#spellchecking)
   - [Enabling / Disabling Spellcheck](#enabling--disabling-spellcheck)
   - [Adding Hunspell](#adding-hunspell)
   - [setSpellChecker()](#setspellchecker)
   - [getSpellChecker()](#getspellchecker)
   - [setSpellCheckEnabled()](#setspellcheckenabled)
   - [Writing a Custom Spellchecker](#writing-a-custom-spellchecker)
7. [Events](#events)
   - [change](#change)
   - [custom-button-click](#custom-button-click)
8. [CSS Variables](#css-variables)
   - [Toolbar Variables](#toolbar-variables)
   - [Button Variables](#button-variables)
   - [Content Area Variables](#content-area-variables)
   - [Editor Container Variables](#editor-container-variables)
   - [Scrollbar Variables](#scrollbar-variables)
   - [Blockquote Variables](#blockquote-variables)
   - [Code / Pre Variables](#code--pre-variables)
   - [Modal / Dialog Variables](#modal--dialog-variables)
9. [Theming with CSS Classes](#theming-with-css-classes)
   - [Fluent 2 Themes](#fluent-2-themes)
10. [Preview Window Styling](#preview-window-styling)
11. [Keyboard Shortcuts](#keyboard-shortcuts)
12. [Accessibility](#accessibility)
13. [Multiple Instances](#multiple-instances)
14. [Browser Support](#browser-support)

---

## Files

| File       | Purpose                                                                                                                                                       |
|:-----------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------|
|rt-native.js|**The only required file.** Contains the complete editor engine, web component wrapper, all CSS defaults, and all dialog styles — everything is self-contained.|

---

## Installation

**npm**

```bash
npm install rt-native
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script type="module">
        import '/node_modules/rt-native/rt-native.js';
    </script>
</head>
<body>
    <rt-native id="editor" height="400px"></rt-native>
</body>
</html>
```

**CDN — unpkg**

```html
<script src="https://unpkg.com/rt-native/rt-native.js"></script>
```

**CDN — jsDelivr**

```html
<script src="https://cdn.jsdelivr.net/npm/rt-native/rt-native.js"></script>
```

---

## Quick Start

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>

    <rt-native id="editor" height="400px"></rt-native>

    <script src="rt-native.js"></script>
    <script>
        const editor = document.getElementById('editor');

        // Read the content
        console.log(editor.getValue());

        // Write content
        editor.setValue('<p>Hello <strong>world</strong></p>');
    </script>
</body>
</html>
```

---

## HTML Attributes

| Attribute | Type             | Default          | Description                                                               |
|:----------|:-----------------|:-----------------|:--------------------------------------------------------------------------|
|value      |string            |''                |Initial HTML content of the editor.                                        |
|width      |string            |100%              |Editor width. Any valid CSS value (px, %, vw, etc.).                       |
|height     |string            |300px             |Editor height. Any valid CSS value.                                        |
|placeholder|string            |—                 |Placeholder text shown when the editor is empty.                           |
|readonly   |boolean (presence)|—                 |Puts the editor in read-only mode. Hides the toolbar.                      |
|aria-label |string            |'Rich text editor'|Accessible name for the editor region.                                     |
|label      |string            |—                 |Alternative to **aria-label** for the accessible name.                     |
|config     |JSON string       |—                 |Declarative configuration. Parsed and passed to **configure()** on connect.|

### Example

```html
<!-- Inline value and placeholder -->
<rt-native value="<p>Hello</p>" width="800px" height="500px"
           placeholder="Start typing…"></rt-native>

<!-- Read-only display -->
<rt-native readonly value="<p>This content cannot be edited.</p>"></rt-native>

<!-- Accessible label -->
<rt-native aria-label="Article body"></rt-native>
```

---

## JavaScript API

### getValue()

Returns the current editor content as an **HTML string**.

```js
const html = editor.getValue();
// "<p>Hello <strong>world</strong></p>"
```

### getPlainText()

Returns the editor content as **plain text** (all HTML tags stripped).

```js
const text = editor.getPlainText();
// "Hello world"
```

### setValue()

Replaces the editor content with the supplied HTML string. Passing an empty string or null clears the editor.

```js
editor.setValue('<h2>New content</h2><p>Paragraph text.</p>');
editor.setValue('');   // clear
```

### configure()

Controls which toolbar buttons are rendered. All buttons are visible by default except the **status bar** (wordCount), which is hidden by default and revealed with the Toggle Status Bar button.

Use `clearAll: true` to hide everything first, then opt individual buttons back in.

```js
// Hide specific buttons
editor.configure({
    visibility: {
        embedMedia:  false,
        table:       false,
        imageUpload: false,
    }
});

// Pass every key explicitly as true to restore full toolbar
editor.configure({
    visibility: {
        clearAll: true,
        font: true, size: true, format: true, insert: true, textStylesDivider: true,
        bold: true, italic: true, underline: true, strikethrough: true,
        subscript: true, superscript: true, formatDivider: true,
        textColor: true, textColorDivider: true,
        alignLeft: true, alignCenter: true, alignRight: true,
        alignJustify: true, alignDivider: true,
        copy: true, cut: true, paste: true, delete: true,
        selectAll: true, actionDivider: true,
        orderedList: true, unorderedList: true, indent: true, listDivider: true,
        link: true, image: true, imageUpload: true,
        quote: true, codeBlock: true, embedMedia: true, video: true, audio: true,
        table: true, horizontalRule: true, mediaDivider: true,
        undo: true, redo: true, historyDivider: true,
        saveHtml: true, htmlView: true, preview: true, fullscreen: true, statusBarToggle: true,
        wordCount: true,
    }
});
```

**All visibility keys:**

| Key             | Controls                                              |
|:----------------|:------------------------------------------------------|
|font             |Font family dropdown                                   |
|size             |Font size dropdown                                     |
|format           |Paragraph / heading format dropdown                    |
|insert           |Insert symbol / emoji dropdown                         |
|textStylesDivider|Divider after the top dropdowns                        |
|bold             |Bold button                                            |
|italic           |Italic button                                          |
|underline        |Underline button                                       |
|strikethrough    |Strikethrough button                                   |
|subscript        |Subscript button                                       |
|superscript      |Superscript button                                     |
|inlineCode       |Inline Code button                                     |
|formatDivider    |Divider after text-format buttons                      |
|textColor        |Text color, background color, and remove-color buttons |
|textColorDivider |Divider after color buttons                            |
|alignLeft        |Align left button                                      |
|alignCenter      |Align center button                                    |
|alignRight       |Align right button                                     |
|alignJustify     |Justify button                                         |
|alignDivider     |Divider after alignment buttons                        |
|cut              |Cut button                                             |
|copy             |Copy button                                            |
|paste            |Paste button                                           |
|delete           |Delete button                                          |
|selectAll        |Select all button                                      |
|actionDivider    |Divider after clipboard buttons                        |
|orderedList      |Ordered list button                                    |
|unorderedList    |Unordered list button                                  |
|indent           |Increase / decrease indent buttons                     |
|listDivider      |Divider after list buttons                             |
|link             |Insert link and remove link buttons                    |
|image            |Insert image button                                    |
|imageUpload      |Upload / embed image button                            |
|quote            |Block quote button                                     |
|codeBlock        |Code block button                                      |
|embedMedia       |Embed media (audio / PDF / iframe) button              |
|video            |Video embed button                                     |
|audio            |Audio embed button                                     |
|table            |Insert table button                                    |
|horizontalRule   |Insert horizontal rule button                          |
|mediaDivider     |Divider after insert buttons                           |
|undo             |Undo button                                            |
|redo             |Redo button                                            |
|historyDivider   |Divider after undo / redo                              |
|statusBarToggle  |Toggle Status Bar button                               |
|saveHtml         |Save HTML file button                                  |
|htmlView         |Toggle HTML source view button                         |
|preview          |Preview button                                         |
|fullscreen       |Maximize / restore editor button                       |
|wordCount        |Status bar (word / character count) — hidden by default|

> **Divider auto-hiding:** Dividers are only rendered when at least one button in their group is visible *and* the divider's own key is true.

The **Audio** toolbar button opens an Audio dialog that inserts `<audio>` elements with one primary source, an optional fallback source, dynamically generated source MIME types, and the browser fallback text `Your browser does not support the audio element.` Hide it with `visibility.audio = false`.

### setReadOnly()

Programmatically enable or disable read-only mode. When read-only, the toolbar is hidden and the content area cannot be edited.

```js
editor.setReadOnly(true);   // lock
editor.setReadOnly(false);  // unlock
```

The **readOnly** getter reflects the current state:

```js
if (editor.readOnly) {
    console.log('Editor is locked.');
}
```

Read-only mode can also be set declaratively via the **readonly** HTML attribute:

```html
<rt-native readonly></rt-native>
```

### setPreviewCssFiles()

Sets one or more CSS files to apply to the preview window. Rules are automatically scoped so they only style the HTML being edited — the toolbar, menus, and dialogs are never affected. Call with no arguments to remove all files.

```js
// Load two stylesheets
editor.setPreviewCssFiles('/styles/content.css', '/styles/tables.css');

// Load a single file
editor.setPreviewCssFiles('/styles/my-content.css');
```

> **CORS:** Files are loaded with fetch(). They must be served from the same origin or include appropriate Access-Control-Allow-Origin headers.

### setPreviewCssFile()

Convenience method that sets a **single** CSS file. Equivalent to **setPreviewCssFiles(url)**.

```js
editor.setPreviewCssFile('/styles/content.css');

// Clear
editor.setPreviewCssFile('');
```

### setPreviewCss()

Supplies **inline CSS** to apply to both the preview window. Rules are automatically scoped, exactly like **setPreviewCssFiles()**. Call with no argument (or '') to clear.

```js
editor.setPreviewCss(`
    h1, h2, h3 { color: #0a2540; }
    blockquote {
        border-left-color: #635bff;
        background: #f8f6ff;
    }
`);
```

**setPreviewCss()** and **setPreviewCssFiles()** are independent — both can be active at the same time. File rules are applied first; inline rules are appended after, so inline CSS always wins when there is a conflict.

###

### addCustomButton()

Adds a single custom button to the right end of the toolbar (after the built-in buttons). If a button with the same `id` already exists it is replaced in-place. The toolbar rebuilds automatically.

```js
editor.addCustomButton({
    id:      'my-stamp',
    title:   'Insert Stamp',
    svg:     `<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 -960 960 960">
                <path d="M160-120v-80h640v80H160Zm0-120v-80l320-400-320-400v-80h640v80L480-720l320 400v80H160Z"/>
              </svg>`,
    onClick: (event, editor) => {
        editor.setValue(editor.getValue() + '<p>🔖 Stamp inserted.</p>');
    }
});
```

| Property | Type      | Required | Description                                         |                                                                                                                                                     |
|:---------|:----------|:---------|:----------------------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------|
|id        |string     |yes       |Unique identifier for the button.                    |                                                                                                                                                     |
|title     |string     |—         |Tooltip text and accessible label. Defaults to `id`. |                                                                                                                                                     |
|svg       |string     |—         |SVG markup rendered as the button icon.              |                                                                                                                                                     |
|onClick   |Function \\|string    |—                                                    |Called when the button is clicked. A string is resolved as a dot-path on `window` (e.g. `'myApp.handleStamp'`). Receives `(event, editor, instance)`.|
|disabled  |boolean    |—         |When `true` the button is rendered but not clickable.|                                                                                                                                                     |

### setCustomButtons()

Replaces **all** custom toolbar buttons at once.

```js
editor.setCustomButtons([
    { id: 'stamp', title: 'Stamp', svg: '…', onClick: handleStamp },
    { id: 'sign',  title: 'Sign',  svg: '…', onClick: handleSign  },
]);
```

### removeCustomButton()

Removes the custom button with the given `id`.

```js
editor.removeCustomButton('stamp');
```

### clearCustomButtons()

Removes **all** custom toolbar buttons.

```js
editor.clearCustomButtons();
```

---

## Spellchecking

rt-native ships **no spellchecker of its own**. Instead it exposes a small pluggable interface — call `setSpellChecker()` with any object that can check and suggest words, and the editor takes care of the rest:

- Misspelled words are underlined with a wavy squiggle using the [CSS Custom Highlight API](https://developer.mozilla.org/en-US/docs/Web/API/CSS_Custom_Highlight_API) (no DOM mutation, so your HTML output is never touched).
- Right-clicking a misspelled word adds a **Spelling** section to the context menu, with a dropdown of suggested corrections plus **Ignore Word** and **Add to Dictionary** actions.
- Everything works fully offline once a spellchecker (such as Hunspell, below) is loaded.

### Enabling / Disabling Spellcheck

Spellcheck marking only runs once a spellchecker has been supplied via `setSpellChecker()` (see below). The `spellCheckEnabled` option turns the marking on or off without discarding the configured spellchecker:

```js
// Declaratively, via configure()
editor.configure({ spellCheckEnabled: false });

// Or imperatively
editor.setSpellCheckEnabled(false); // turn off
editor.setSpellCheckEnabled(true);  // turn back on
```

It defaults to `true`, so once a spellchecker is configured, checking starts immediately.

### Adding Hunspell

The real [Hunspell](https://github.com/hunspell/hunspell) engine, compiled to WebAssembly, is available on the [hunspell-asm](https://www.npmjs.com/package/hunspell-asm) npm package, served via the [jsDelivr](https://www.jsdelivr.com/) CDN — no download, npm install, or bundler required. Pair it with a CDN-hosted dictionary such as [hunspell-dict-en-us](https://www.npmjs.com/package/hunspell-dict-en-us) and a small adapter that wires Hunspell into rt-native's `setSpellChecker()` interface.

Add the WASM engine as a plain `<script>` tag (defines `window.Module`), **before** rt-native itself:

```html
<script src="https://cdn.jsdelivr.net/npm/hunspell-asm@4.0.2/dist/cjs/lib/browser/hunspell.js"></script>
<script src="rt-native.js"></script>
```

Then add the loader and the rt-native adapter. Both are small, dependency-free, and safe to paste directly into your page — no separate files to host:

```html
<script>
/**
 * Dependency-free browser loader for the Hunspell WASM engine loaded above.
 * A no-bundler port of the loading logic used by the "hunspell-asm" npm
 * package (MIT licensed), with its two small dependencies
 * ("emscripten-wasm-loader" and "nanoid") inlined.
 */
(function (global) {
    'use strict';

    const randomId = (length) => {
        const chars = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
        let out = '';
        for (let i = 0; i < length; i++) out += chars[Math.floor(Math.random() * chars.length)];
        return out;
    };

    const isMounted = (FS, path, type) => {
        try {
            const stat = FS.stat(path);
            const check = type === 'dir' ? FS.isDir : FS.isFile;
            return !!stat && check(stat.mode);
        } catch (_) {
            return false;
        }
    };

    /* Builds the overrides object passed into the Emscripten MODULARIZE
       factory function, exposing an awaitable initializeRuntime() that
       resolves once Module["onRuntimeInitialized"] fires. */
    const constructModule = () => {
        const ret = { __asmModuleInitialized__: false, onRuntimeInitialized: null };
        ret.initializeRuntime = (timeout) => {
            if (ret.__asmModuleInitialized__) return Promise.resolve(true);
            return new Promise((resolve) => {
                const timeoutId = timeout ? setTimeout(() => resolve(false), timeout) : null;
                ret.onAbort = (reason) => {
                    if (!ret.__asmModuleInitialized__) {
                        if (timeoutId) clearTimeout(timeoutId);
                        throw reason instanceof Error ? reason : new Error(reason);
                    }
                };
                ret.onRuntimeInitialized = () => {
                    if (timeoutId) clearTimeout(timeoutId);
                    ret.__asmModuleInitialized__ = true;
                    resolve(true);
                };
            });
        };
        return ret;
    };

    /* Hunspell's C API, wrapped via Emscripten's cwrap. Signatures mirror
       hunspell/src/hunspell/hunspell.hxx's extern "C" wrapper functions. */
    const wrapHunspellInterface = (cwrap) => ({
        create: cwrap('Hunspell_create', 'number', ['number', 'number']),
        destroy: cwrap('Hunspell_destroy', null, ['number']),
        spell: cwrap('Hunspell_spell', 'number', ['number', 'number']),
        suggest: cwrap('Hunspell_suggest', 'number', ['number', 'number', 'number']),
        free_list: cwrap('Hunspell_free_list', null, ['number', 'number', 'number']),
        add_dic: cwrap('Hunspell_add_dic', 'number', ['number', 'number']),
        add: cwrap('Hunspell_add', 'number', ['number', 'number']),
        add_with_affix: cwrap('Hunspell_add_with_affix', 'number', ['number', 'number', 'number']),
        remove: cwrap('Hunspell_remove', 'number', ['number', 'number'])
    });

    const hunspellLoader = (asmModule) => {
        const { cwrap, FS, _free, allocateUTF8, _malloc, getValue, UTF8ToString } = asmModule;
        const hunspellInterface = wrapHunspellInterface(cwrap);
        const memPathId = `/${randomId(24)}`;
        FS.mkdir(memPathId);

        const usingParamPtr = (...args) => {
            const params = [...args];
            const fn = params.pop();
            const paramsPtr = params.map((param) => allocateUTF8(param.normalize()));
            const ret = fn(...paramsPtr);
            paramsPtr.forEach((ptr) => _free(ptr));
            return ret;
        };

        return {
            mountBuffer: (contents, fileName) => {
                const file = fileName || randomId(24);
                const mountedFilePath = `${memPathId}/${file}`;
                if (!isMounted(FS, mountedFilePath, 'file')) {
                    FS.writeFile(mountedFilePath, contents, { encoding: 'binary' });
                }
                return mountedFilePath;
            },
            unmount: (mountedPath) => {
                if (isMounted(FS, mountedPath, 'file') && mountedPath.indexOf(memPathId) > -1) {
                    FS.unlink(mountedPath);
                    return;
                }
                if (isMounted(FS, mountedPath, 'dir')) {
                    FS.unmount(mountedPath);
                    FS.rmdir(mountedPath);
                }
            },
            create: (affPath, dictPath) => {
                const affPathPtr = allocateUTF8(affPath);
                const dictPathPtr = allocateUTF8(dictPath);
                const hunspellPtr = hunspellInterface.create(affPathPtr, dictPathPtr);
                return {
                    dispose: () => {
                        hunspellInterface.destroy(hunspellPtr);
                        _free(affPathPtr);
                        _free(dictPathPtr);
                    },
                    spell: (word) => !!usingParamPtr(word, (wordPtr) => hunspellInterface.spell(hunspellPtr, wordPtr)),
                    suggest: (word) => {
                        const suggestionListPtr = _malloc(4);
                        const suggestionCount = usingParamPtr(word, (wordPtr) =>
                            hunspellInterface.suggest(hunspellPtr, suggestionListPtr, wordPtr));
                        const suggestionListValuePtr = getValue(suggestionListPtr, '*');
                        const ret = suggestionCount > 0
                            ? Array.from(Array(suggestionCount).keys()).map((idx) =>
                                UTF8ToString(getValue(suggestionListValuePtr + idx * 4, '*')))
                            : [];
                        hunspellInterface.free_list(hunspellPtr, suggestionListPtr, suggestionCount);
                        _free(suggestionListPtr);
                        return ret;
                    },
                    addDictionary: (dictPath) => usingParamPtr(dictPath, (dictPathPtr) =>
                        hunspellInterface.add_dic(hunspellPtr, dictPathPtr)) !== 1,
                    addWord: (word) => usingParamPtr(word, (wordPtr) => hunspellInterface.add(hunspellPtr, wordPtr)),
                    addWordWithAffix: (word, affix) => usingParamPtr(word, affix, (wordPtr, affixPtr) =>
                        hunspellInterface.add_with_affix(hunspellPtr, wordPtr, affixPtr)),
                    removeWord: (word) => usingParamPtr(word, (wordPtr) => hunspellInterface.remove(hunspellPtr, wordPtr))
                };
            }
        };
    };

    const loadModule = async (initOptions) => {
        const timeout = (initOptions && initOptions.timeout) || 20000;
        if (typeof global.Module !== 'function') {
            throw new Error(
                'HunspellAsm.loadModule: window.Module was not found. ' +
                'Include the hunspell-asm <script> tag before this one.'
            );
        }
        const constructedModule = constructModule();
        const asmModule = global.Module(constructedModule);
        const result = await asmModule.initializeRuntime(timeout);
        if (!result) {
            throw new Error('HunspellAsm.loadModule: timed out initializing the WebAssembly runtime.');
        }
        return hunspellLoader(asmModule);
    };

    global.HunspellAsm = { loadModule };
})(typeof window !== 'undefined' ? window : this);
</script>

<script>
/**
 * The real Hunspell spellchecker (https://github.com/hunspell/hunspell),
 * compiled to WebAssembly and running in the browser, wired up as an
 * rt-native spellchecker adapter:
 *
 *   checkWord(word) => Promise<boolean>
 *   suggest(word)   => Promise<string[]>
 *   addWord(word)   => void
 *   ignoreWord(word)=> void
 *
 * Loading the WASM engine is asynchronous, so every method waits on an
 * internal "ready" promise before touching the Hunspell instance - callers
 * can configure the checker immediately via rt-native's setSpellChecker()
 * without waiting for it to finish loading.
 */
(function (global) {
    'use strict';

    /* The WASM runtime itself is shared across instances - it's the slow
       part to load (~1s) and holds no dictionary-specific state. */
    let sharedFactoryPromise = null;
    const getFactory = () => {
        if (!sharedFactoryPromise) {
            if (!global.HunspellAsm || typeof global.HunspellAsm.loadModule !== 'function') {
                return Promise.reject(new Error(
                    'HunspellSpellChecker: window.HunspellAsm was not found. ' +
                    'Include the hunspell-asm <script> tag and the loader script above before this one.'
                ));
            }
            sharedFactoryPromise = global.HunspellAsm.loadModule();
        }
        return sharedFactoryPromise;
    };

    class HunspellSpellChecker {
        /**
         * @param {object} [options]
         * @param {{ aff: string, dic: string }} [options.dictionary] - Defaults to
         *   window.HunspellDictionaries.en_US, fetched from a CDN dictionary
         *   package such as hunspell-dict-en-us (see the setup below).
         */
        constructor(options = {}) {
            const dictionary = options.dictionary
                || (global.HunspellDictionaries && global.HunspellDictionaries.en_US);
            if (!dictionary || !dictionary.aff || !dictionary.dic) {
                throw new Error(
                    'HunspellSpellChecker: no dictionary supplied and window.HunspellDictionaries.en_US ' +
                    'was not found. Fetch a dictionary from a CDN and set window.HunspellDictionaries.en_US ' +
                    '(see the setup below) before constructing HunspellSpellChecker.'
                );
            }

            this._hunspell = null;
            this._ignored = new Set();
            this._ready = this._load(dictionary).catch((error) => {
                console.error('HunspellSpellChecker: failed to load the Hunspell WASM engine or dictionary.', error);
                throw error;
            });
        }

        async _load(dictionary) {
            const factory = await getFactory();
            const encoder = new TextEncoder();
            const affPath = factory.mountBuffer(encoder.encode(dictionary.aff), 'index.aff');
            const dicPath = factory.mountBuffer(encoder.encode(dictionary.dic), 'index.dic');
            this._hunspell = factory.create(affPath, dicPath);
        }

        /** Resolves once the WASM engine and dictionary have finished loading. */
        get ready() {
            return this._ready;
        }

        async checkWord(word) {
            await this._ready;
            if (this._ignored.has(String(word).toLowerCase())) return true;
            return this._hunspell.spell(word);
        }

        async suggest(word) {
            await this._ready;
            return this._hunspell.suggest(word);
        }

        addWord(word) {
            if (!word) return;
            this._ready.then(() => this._hunspell.addWord(word)).catch(() => {});
        }

        ignoreWord(word) {
            if (!word) return;
            this._ignored.add(String(word).toLowerCase());
        }
    }

    global.HunspellSpellChecker = HunspellSpellChecker;
})(typeof window !== 'undefined' ? window : this);
</script>
```

Finally, fetch the dictionary from its CDN and register it on `window.HunspellDictionaries.en_US`. Once that's done, `new HunspellSpellChecker()` (used throughout the rest of this section) picks it up automatically:

```js
const dictBase = 'https://cdn.jsdelivr.net/npm/hunspell-dict-en-us@0.1.0';

const [aff, dic] = await Promise.all([
    fetch(`${dictBase}/en-us.aff`).then(r => r.text()),
    fetch(`${dictBase}/en-us.dic`).then(r => r.text()),
]);
window.HunspellDictionaries = { en_US: { aff, dic } };

const editor = document.getElementById('editor');
editor.setSpellChecker(new HunspellSpellChecker());
```

`HunspellSpellChecker` loads the WASM engine the first time it's needed, so `setSpellChecker()` can be called immediately once the dictionary fetch above has resolved. A dictionary can also be passed directly instead of relying on the global, by constructing with `new HunspellSpellChecker({ dictionary: { aff, dic } })`.

To use a different language, swap in another CDN dictionary package (e.g. [hunspell-dict-fr-fr](https://www.npmjs.com/package/hunspell-dict-fr-fr)) or any other source of raw `.aff`/`.dic` text — the `dictionary` option only needs the two strings, however you obtain them.

| Online resource                                                                        | Purpose                                                                                                |
|:---------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------|
|[jsDelivr](https://www.jsdelivr.com/): `hunspell-asm` `dist/cjs/lib/browser/hunspell.js`|The Hunspell C++ engine compiled to WebAssembly (defines `window.Module`)                               |
|[jsDelivr](https://www.jsdelivr.com/): `hunspell-dict-en-us` `en-us.aff` / `en-us.dic`  |The `en_US` dictionary in raw Hunspell format, fetched at runtime with `fetch()`                        |
|Loader script (inlined above)                                                           |Dependency-free browser loader for the WASM engine (defines `window.HunspellAsm`)                       |
|Adapter script (inlined above)                                                          |Implements rt-native's spellchecker interface on top of Hunspell (defines `window.HunspellSpellChecker`)|

> **License note:** `hunspell-asm` is MIT licensed and `hunspell-dict-en-us` is MIT/BSD licensed; the underlying Hunspell engine itself carries its own MPL/GPL/LGPL tri-license. Other dictionary packages have their own licenses that vary by language (e.g. the French dictionary is MPL-2.0) — review each package's license before distributing your app.

### setSpellChecker()

Configures the active spellchecker. Pass `null` to remove it (clears all squiggles and the Spelling menu).

```js
editor.setSpellChecker(new HunspellSpellChecker());

// Remove it
editor.setSpellChecker(null);
```

### getSpellChecker()

Returns the currently configured spellchecker, or `null`.

```js
const checker = editor.getSpellChecker();
```

### setSpellCheckEnabled()

Enables or disables spellcheck marking without clearing the configured spellchecker. See [Enabling / Disabling Spellcheck](#enabling--disabling-spellcheck) above.

```js
editor.setSpellCheckEnabled(false);
```

### Writing a Custom Spellchecker

`setSpellChecker()` accepts any object matching this duck-typed interface — Hunspell is just one implementation:

| Method         | Required | Description                                                                                           |
|:---------------|:---------|:------------------------------------------------------------------------------------------------------|
|checkWord(word) |yes       |Returns (or resolves to) `true`/`false` — whether `word` is spelled correctly.                         |
|suggest(word)   |—         |Returns (or resolves to) a `string[]` of suggested corrections, used to populate the Spelling dropdown.|
|addWord(word)   |—         |Called when the user chooses **Add to Dictionary**. Enables that menu item.                            |
|ignoreWord(word)|—         |Called when the user chooses **Ignore Word**, in addition to rt-native's own internal ignore list.     |

```js
editor.setSpellChecker({
    checkWord: async (word) => myDictionary.has(word.toLowerCase()),
    suggest:   async (word) => myDictionary.suggest(word),
    addWord:   (word) => myDictionary.add(word),
});
```

`checkWord` and `suggest` may return a plain value or a `Promise` — both are awaited.

---

## Events

### change

Fired on the element whenever the editor content changes. The event **bubbles** and is **composed** (crosses shadow DOM boundaries).

```js
editor.addEventListener('change', (event) => {
    const html = event.detail.value;  // current HTML string
    console.log(html);
});
```

| Property         | Value                         |
|:-----------------|:------------------------------|
|event.detail.value|Current editor HTML as a string|
|event.bubbles     |true                           |
|event.composed    |true                           |

### custom-button-click

Fired whenever a custom toolbar button is clicked. The event **bubbles** and is **composed**.

```js
editor.addEventListener('custom-button-click', (event) => {
    console.log('Custom button clicked:', event.detail.id);
});
```

| Property          | Value                                 |
|:------------------|:--------------------------------------|
|event.detail.id    |The `id` of the button that was clicked|
|event.detail.button|The full button definition object      |
|event.detail.editor|The `rt-native` element                |
|event.bubbles      |true                                   |
|event.composed     |true                                   |

---

## CSS Variables

All visual aspects of the editor are controlled through CSS custom properties declared on the **rt-native** element. Default values are injected automatically by **rt-native.js** when the first editor mounts — no stylesheet required. Override any variable in your own CSS to theme the editor.

```css
/* Override globally (all editors on the page) */
rt-native {
    --rtb-toolbar-bg: #f5f5f5;
    --rtb-btn-size:   14px;
}

/* Override for a specific editor */
#my-editor {
    --rtb-content-bg:   #1e1e1e;
    --rtb-content-text: #ddd;
}
```

> **How it works:** CSS custom properties inherit through shadow boundaries. Any value set on the host element is automatically picked up inside the shadow root — no re-initialization needed.

---

### Toolbar Variables

| Variable                  | Default | Description                                                     |
|:--------------------------|:--------|:----------------------------------------------------------------|
|--rtb-toolbar-bg           |#FFF     |Toolbar background color                                         |
|--rtb-toolbar-border-style |solid    |Toolbar bottom border style                                      |
|--rtb-toolbar-border-width |1px      |Toolbar bottom border width                                      |
|--rtb-toolbar-border-color |#EEE     |Toolbar bottom border color                                      |
|--rtb-toolbar-border-radius|0px      |Toolbar corner radius (container clips top corners automatically)|
|--rtb-dropdown-bg          |#FFF     |Font / Size / Format dropdown background                         |
|--rtb-dropdown-text        |#000     |Dropdown item text color                                         |
|--rtb-dropdown-bg-hover    |#e5e5e5  |Dropdown item hover background                                   |
|--rtb-dropdown-text-hover  |#000     |Dropdown item hover text color                                   |

---

### Button Variables

| Variable                | Default         | Description                                                |
|:------------------------|:----------------|:-----------------------------------------------------------|
|--rtb-btn-text           |#000             |Button icon color                                           |
|--rtb-btn-size           |16px             |Icon size (also drives button min-height and divider height)|
|--rtb-btn-font           |Arial, sans-serif|Font for dropdown buttons                                   |
|--rtb-btn-bg             |inherit          |Button background at rest                                   |
|--rtb-btn-bg-hover       |#DDD             |Button background on hover                                  |
|--rtb-btn-bg-selected    |#CCC             |Button background when active / selected                    |
|--rtb-btn-border-style   |none             |Button border style                                         |
|--rtb-btn-border-width   |0px              |Button border width                                         |
|--rtb-btn-border-color   |#AAA             |Button border color at rest                                 |
|--rtb-btn-border-hover   |inherit          |Button border color on hover                                |
|--rtb-btn-border-selected|inherit          |Button border color when selected                           |
|--rtb-btn-border-radius  |5px              |Button corner radius                                        |

---

### Content Area Variables

| Variable                 | Default         | Description                                                                   |
|:-------------------------|:----------------|:------------------------------------------------------------------------------|
|--rtb-content-text        |#000             |Editor text color                                                              |
|--rtb-content-size        |16px             |Editor font size                                                               |
|--rtb-content-font        |Arial, sans-serif|Editor font family                                                             |
|--rtb-content-bg          |#FFF             |Editor content background color                                                |
|--rtb-content-shadow      |none             |Inner box shadow on the content area                                           |
|--rtb-spellcheck-underline|#e5484d          |Wavy underline color for misspelled words (see [Spellchecking](#spellchecking))|
|--rtb-placeholder-color   |#9ca3af          |Placeholder text color                                                         |

---

### Editor Container Variables

| Variable                 | Default | Description                                                                                   |
|:-------------------------|:--------|:----------------------------------------------------------------------------------------------|
|--rtb-editor-width        |100%     |Maximum width of the editor                                                                    |
|--rtb-editor-height       |300px    |Height of the editor                                                                           |
|--rtb-editor-border-style |solid    |Outer border style                                                                             |
|--rtb-editor-border-width |1px      |Outer border width                                                                             |
|--rtb-editor-border-color |#EEE     |Outer border color                                                                             |
|--rtb-editor-border-radius|0px      |Outer corner radius                                                                            |
|--rtb-editor-shadow       |none     |Outer box shadow                                                                               |
|--rtb-editor-resize       |auto     |auto shows the resize handle; hidden removes it                                                |
|--rtb-z-index             |1        |Z-index of the editor container — raise this to stack the editor above surrounding page content|

---

### Scrollbar Variables

| Variable                  | Default   | Description                  |
|:--------------------------|:----------|:-----------------------------|
|--rtb-scroll-width         |10px       |Scrollbar track width         |
|--rtb-scroll-opacity       |1          |Scrollbar opacity             |
|--rtb-scroll-bg            |transparent|Scrollbar track background    |
|--rtb-scroll-thumb-bg      |#AAA       |Scrollbar thumb color         |
|--rtb-scroll-thumb-bg-hover|#DDD       |Scrollbar thumb color on hover|
|--rtb-scroll-thumb-radius  |0          |Scrollbar thumb corner radius |

---

### Blockquote Variables

| Variable               | Default | Description                |
|:-----------------------|:--------|:---------------------------|
|--rtb-quote-bg          |#f9f9f9  |Blockquote background color |
|--rtb-quote-border-color|#ccc     |Blockquote left-border color|
|--rtb-quote-border-width|5px      |Blockquote left-border width|

---

### Code / Pre Variables

| Variable               | Default | Description               |
|:-----------------------|:--------|:--------------------------|
|--rtb-code-bg           |#f9f9f9  |Code block background color|
|--rtb-code-border-radius|10px     |Code block corner radius   |

---

### Modal / Dialog Variables

| Variable               | Default         | Description                      |
|:-----------------------|:----------------|:---------------------------------|
|--rtb-modal-bg          |#fefefe          |Dialog background color           |
|--rtb-modal-text        |#000             |Dialog text and close-button color|
|--rtb-modal-text-size   |16px             |Dialog font size                  |
|--rtb-modal-text-font   |Arial, sans-serif|Dialog font family                |
|--rtb-modal-input-bg    |#fff             |Input field background            |
|--rtb-modal-input-text  |#000             |Input field text color            |
|--rtb-modal-input-border|#CCC             |Input field border color          |
|--rtb-modal-checkbox    |#007bff          |Checkbox accent color             |

---

## Theming with CSS Classes

The cleanest way to create reusable themes is to define a CSS class that overrides the variables you need, then apply it to the element.

```css
/* styles.css */
rt-native.dark {
    --rtb-toolbar-bg:          #1e1e1e;
    --rtb-toolbar-border-color:#333;
    --rtb-btn-text:            #ccc;
    --rtb-btn-bg-hover:        #3a3a3a;
    --rtb-btn-bg-selected:     #444;
    --rtb-content-text:        #ddd;
    --rtb-content-bg:          #252526;
    --rtb-editor-border-color: #333;
    --rtb-modal-bg:            #2d2d2d;
    --rtb-modal-text:          #ccc;
    --rtb-modal-input-bg:      #1e1e1e;
    --rtb-modal-input-text:    #ccc;
    --rtb-modal-input-border:  #555;
    --rtb-dropdown-bg:         #2d2d2d;
    --rtb-dropdown-text:       #ccc;
    --rtb-dropdown-bg-hover:   #3a3a3a;
}
```

**Apply via HTML:**

```html
<rt-native class="dark" id="editor" height="400px"></rt-native>
```

**Apply via JavaScript:**

```js
var editor = document.getElementById("editor");
if (editor) {
    if (editor.classList.contains('light')) {
        editor.classList.remove('light');
    }
    if (editor.classList.contains('dark')) {
        editor.classList.remove('dark');
    }
    editor.classList.add(theme);
}
```

**Apply via media query (system dark mode):**

```css
@media (prefers-color-scheme: dark) {
    rt-native {
        --rtb-toolbar-bg:  #1e1e1e;
        --rtb-content-bg:  #252526;
        --rtb-content-text:#ddd;
        /* etc. */
    }
}
```

### Fluent 2 Themes

Complete ready-to-use implementations of Microsoft's [Fluent 2 Design System](https://fluent2.microsoft.design/). Copy either block into your stylesheet and apply the class to the element.

**Fluent 2 Light**

```css
rt-native.fluent {
    /* Typography */
    --rtb-btn-font:              Arial, Helvetica, Verdana, sans-serif;
    --rtb-btn-size:              16px;

    /* Toolbar */
    --rtb-toolbar-bg:            #ffffff;
    --rtb-toolbar-border-style:  solid;
    --rtb-toolbar-border-width:  1px;
    --rtb-toolbar-border-color:  #d1d1d1;
    --rtb-dropdown-bg:           #ffffff;
    --rtb-dropdown-text:         #242424;
    --rtb-dropdown-bg-hover:     #f5f5f5;
    --rtb-dropdown-text-hover:   #242424;

    /* Buttons */
    --rtb-btn-text:              #242424;
    --rtb-btn-bg:                transparent;
    --rtb-btn-bg-hover:          #e8e8e8;
    --rtb-btn-bg-selected:       #dcdcdc;
    --rtb-btn-border-style:      none;
    --rtb-btn-border-radius:     4px;

    /* Content area */
    --rtb-content-text:          #242424;
    --rtb-content-size:          1rem;
    --rtb-content-font:          Arial, Helvetica, Verdana, sans-serif;
    --rtb-content-bg:            #ffffff;

    /* Editor container */
    --rtb-editor-border-style:   solid;
    --rtb-editor-border-width:   1px;
    --rtb-editor-border-color:   #d1d1d1;
    --rtb-editor-border-radius:  4px;
    --rtb-editor-shadow:         0 2px 4px rgba(0, 0, 0, 0.06);

    /* Scrollbars */
    --rtb-scroll-width:          6px;
    --rtb-scroll-thumb-bg:       #c2c2c2;
    --rtb-scroll-thumb-bg-hover: #8a8a8a;
    --rtb-scroll-thumb-radius:   3px;

    /* Modals & dialogs */
    --rtb-modal-bg:              #ffffff;
    --rtb-modal-text:            #242424;
    --rtb-modal-text-size:       1rem;
    --rtb-modal-text-font:       Arial, Helvetica, Verdana, sans-serif;
    --rtb-modal-input-bg:        #ffffff;
    --rtb-modal-input-text:      #242424;
    --rtb-modal-input-border:    #d1d1d1;
    --rtb-modal-checkbox:        #0078d4;

    /* Blockquote */
    --rtb-quote-bg:              #f0f7ff;
    --rtb-quote-border-color:    #0078d4;
    --rtb-quote-border-width:    4px;

    /* Code blocks */
    --rtb-code-bg:               #f5f5f5;
    --rtb-code-border-radius:    4px;
}
```

```html
<rt-native class="fluent" id="editor" height="400px"></rt-native>
```

**Fluent 2 Dark**

```css
rt-native.fluent-dark {
    /* Typography */
    --rtb-btn-font:              Arial, Helvetica, Verdana, sans-serif;
    --rtb-btn-size:              16px;

    /* Toolbar */
    --rtb-toolbar-bg:            #292929;
    --rtb-toolbar-border-style:  solid;
    --rtb-toolbar-border-width:  1px;
    --rtb-toolbar-border-color:  #424242;
    --rtb-dropdown-bg:           #292929;
    --rtb-dropdown-text:         #ffffff;
    --rtb-dropdown-bg-hover:     #2e2e2e;
    --rtb-dropdown-text-hover:   #ffffff;

    /* Buttons */
    --rtb-btn-text:              #ffffff;
    --rtb-btn-bg:                transparent;
    --rtb-btn-bg-hover:          #404040;
    --rtb-btn-bg-selected:       #4e4e4e;
    --rtb-btn-border-style:      none;
    --rtb-btn-border-radius:     4px;

    /* Content area */
    --rtb-content-text:          #ffffff;
    --rtb-content-size:          1rem;
    --rtb-content-font:          Arial, Helvetica, Verdana, sans-serif;
    --rtb-content-bg:            #1f1f1f;

    /* Editor container */
    --rtb-editor-border-style:   solid;
    --rtb-editor-border-width:   1px;
    --rtb-editor-border-color:   #424242;
    --rtb-editor-border-radius:  4px;
    --rtb-editor-shadow:         0 2px 8px rgba(0, 0, 0, 0.32);

    /* Scrollbars */
    --rtb-scroll-width:          6px;
    --rtb-scroll-thumb-bg:       #5c5c5c;
    --rtb-scroll-thumb-bg-hover: #8a8a8a;
    --rtb-scroll-thumb-radius:   3px;

    /* Modals & dialogs */
    --rtb-modal-bg:              #2e2e2e;
    --rtb-modal-text:            #ffffff;
    --rtb-modal-text-size:       1rem;
    --rtb-modal-text-font:       Arial, Helvetica, Verdana, sans-serif;
    --rtb-modal-input-bg:        #1f1f1f;
    --rtb-modal-input-text:      #ffffff;
    --rtb-modal-input-border:    #424242;
    --rtb-modal-checkbox:        #479ef5;

    /* Blockquote */
    --rtb-quote-bg:              #00244a;
    --rtb-quote-border-color:    #479ef5;
    --rtb-quote-border-width:    4px;

    /* Code blocks */
    --rtb-code-bg:               #141414;
    --rtb-code-border-radius:    4px;
}
```

```html
<rt-native class="fluent-dark" id="editor" height="400px"></rt-native>
```

---

## Preview Window Styling

When you load preview CSS with **setPreviewCssFiles()** or **setPreviewCss()**, the component applies the styles to the Preview window. Content is rendered inside an iframe srcdoc with a completely isolated browsing context. The preview shows exactly what a reader would see in production with a clean browser baseline.

```js
editor.setPreviewCssFiles('my-content.css');
```

---

## Keyboard Shortcuts

All shortcuts are active when the editor content area has focus. The Ctrl+\\ and Ctrl+Shift+A/P/S shortcuts also work when the HTML source textarea has focus.

| Category     | Action              | Shortcut                  |
|:-------------|:--------------------|:--------------------------|
|**Formatting**|Bold                 |Ctrl+B                     |
|              |Italic               |Ctrl+I                     |
|              |Underline            |Ctrl+U                     |
|              |Strikethrough        |Ctrl+D                     |
|              |Subscript            |Ctrl+=                     |
|              |Superscript          |Ctrl+Shift++               |
|**Color**     |Text color           |Ctrl+Shift+C               |
|              |Text background color|Ctrl+Shift+B               |
|**Alignment** |Align left           |Ctrl+L                     |
|              |Align center         |Ctrl+E                     |
|              |Align right          |Ctrl+R                     |
|              |Justify              |Ctrl+J                     |
|**Editing**   |Cut                  |Ctrl+X                     |
|              |Copy                 |Ctrl+C                     |
|              |Paste                |Ctrl+V                     |
|              |Select all           |Ctrl+A                     |
|              |Undo                 |Ctrl+Z                     |
|              |Redo                 |Ctrl+Y                     |
|**Lists**     |Ordered list         |Ctrl+Shift+O               |
|              |Unordered list       |Ctrl+Shift+U               |
|              |Increase indent      |Tab                        |
|              |Decrease indent      |Shift+Tab                  |
|**Insert**    |Insert link          |Ctrl+Shift+K               |
|              |Insert image         |Ctrl+Shift+I               |
|              |Upload image         |Ctrl+Shift+&amp;           |
|              |Block quote          |Ctrl+Shift+Q               |
|              |Video                |Ctrl+Shift+V               |
|              |Audio                |Ctrl+Shift+S               |
|              |Embed media          |Ctrl+Shift+M               |
|              |Insert table         |Ctrl+Shift+L               |
|              |Code block           |Ctrl+Shift+\*              |
|              |Horizontal rule      |Ctrl+Shift+H               |
|**Format**    |Paragraph            |Ctrl+Shift+D               |
|              |Heading 1–6          |Ctrl+Shift+1 – Ctrl+Shift+6|
|              |Increase font size   |Ctrl+Shift+>               |
|              |Decrease font size   |Ctrl+Shift+<               |
|**View**      |Toggle status bar    |Ctrl+\\                    |
|              |Toggle HTML source   |Ctrl+Shift+A               |
|              |Preview              |Ctrl+Shift+P               |
|              |Save HTML            |Ctrl+Shift+S               |

---

## Accessibility

**rt-native** is built with WCAG 2.1 AA compliance in mind:

- **Editor region** — The content area carries role="textbox", aria-multiline="true", and an aria-label (defaults to "Rich text editor"; override with the **aria-label** or **label** attribute on the host element).
- **Read-only state** — aria-readonly is kept in sync with the **readonly** attribute and **setReadOnly()**.
- **Toolbar** — The toolbar container has role="toolbar" and aria-label="Formatting toolbar". Every button has an aria-label derived from its tooltip text (e.g. "Bold (Ctrl+B)") and an aria-pressed attribute that is kept in sync with the button's active/selected state.
- **Status bar** — Carries role="status", aria-live="polite", and aria-atomic="true" so word and character count updates are announced non-intrusively by screen readers.
- **Dialogs** — Every dialog has aria-modal="true" and aria-labelledby pointing to its title element. Close buttons are native button elements with descriptive aria-label text.
- **HTML source textarea** — Has aria-label="HTML source" to distinguish it from the main editor.

---

## Multiple Instances

Each rt-native element is fully isolated. You can place as many on a page as needed — each gets its own unique ID, shadow root, and state.

```html
<rt-native id="editor-1" height="200px"></rt-native>
<rt-native id="editor-2" height="200px" class="dark"></rt-native>
<rt-native id="editor-3" height="200px"></rt-native>

<script src="rt-native.js"></script>
<script>
    document.getElementById('editor-1').configure({
        visibility: { clearAll: true, bold: true, italic: true }
    });
    document.getElementById('editor-2').setPreviewCssFiles('/styles/content.css');
</script>
```

---

## Browser Support

Requires browsers with native support for:

- [Custom Elements v1](https://caniuse.com/custom-elementsv1)
- [Shadow DOM v1](https://caniuse.com/shadowdomv1)
- [dialog element](https://caniuse.com/dialog)
- [CSS Custom Properties](https://caniuse.com/css-variables)
- [fetch()](https://caniuse.com/fetch) *(required for **setPreviewCssFiles()** / **setPreviewCssFile()**)*

All modern browsers (Chrome 67+, Firefox 63+, Safari 12.1+, Edge 79+) are supported. Internet Explorer is not supported.