# monaco-monarch-jsx

This is a simple tweak to the Monarch definition of the Typescript/Javascript syntax highlighting rules to add support for JSX. It's not guaranteed correct— instead it's just something that mostly looks right. 

It turns out that getting Monaco to syntax highlight JSX is much [more complicated than one might expect](https://github.com/Microsoft/monaco-editor/issues/264). Note that it's comparatively simple to add support for the TypeScript language server with support for JSX. 

The approach used by an [old version of CodeSandbox](https://github.com/codesandbox/codesandbox-client/blob/196301c919dd032dccc08cbeb48cf8722eadd36b/packages/app/src/app/components/CodeEditor/Monaco/workers/syntax-highlighter.js), [Expo](https://blog.expo.io/building-a-code-editor-with-monaco-f84b3a06deaf), [jsx-explorer](https://github.com/cancerberoSgx/jsx-alone/blob/master/jsx-explorer/HOWTO_JSX_MONACO.md) is to create a new WebWorker which contains the Typescript parser— and periodically interpreting the resulting AST to highlight the text. 

I believe [alm](https://github.com/alm-tools/alm/blob/c77c89c5efb3b6ef6f978df2b9f5b76540cb25e0/src/app/monaco/languages/typescript/tokenization.ts) used another syntax highlighting library (prism.js) in order to highlight JSX + JS. 

Visual Studio Code is built on top of Monaco— but it doesn't use the same syntax highlighting engine (Monarch) as the web. Instead it uses [vscode-textmate](https://github.com/microsoft/vscode-textmate) which is an implementation of the TextMate syntax definition language (which can be encoded either as JSON or an XML plist). The textmate syntax definition language is pretty entwined with the Oniguruma RegExp library— which is written in C. 

Modern versions of CodeSandbox use a [WebAssembly version of Oniguruma](https://github.com/NeekSandhu/onigasm) in order to replicate the VS Code native experience on the web. Unfortunately it's a bit complicated and unwieldy to set up and also has a fair amount of overhead. 

This repo contains a small tweak to the [monarch typescript syntax definition file](https://github.com/microsoft/monaco-languages/blob/master/src/typescript/typescript.ts) that allows it to parse JSX syntax.

```js
import { registerLanguage } from 'monaco-editor/esm/vs/basic-languages/_.contribution.js';

registerLanguage({
    id: 'typescript',
    extensions: ['.ts', '.tsx'],
    aliases: ['TypeScript', 'ts', 'typescript'],
    mimetypes: ['text/typescript'],
    loader: function () { return import('./typescript-jsx.js'); }
});
```
