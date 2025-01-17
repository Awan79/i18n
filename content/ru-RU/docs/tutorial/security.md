# Безопасность, нативные возможности и ваша ответственность

Как веб-разработчики, мы заботимся о безопасности браузера и риски, связанные с кодом, который мы пишем, достаточно небольшие. Наши веб-сайты имеют ограниченные полномочия в песочнице, и мы верим, что пользователи довольны браузером, созданным большой командой инженеров, которая может быстро реагировать на последние обнаруженные угрозы безопасности.

Когда вы работаете в среде Электрон, важно понимать, что это не веб браузер. Электрон позволяет создавать функционально развитые приложения для настольных компьютеров с помощью веб технологий, но ваш код обладает большими возможностями. JavaScript имеет доступ к файловой системе, пользовательским скриптам и т. д. Благодаря этим возможностям вы можете создавать высококачественные нативные приложения, но это так же множит риски увеличивающиеся с дополнительными полномочиями вашего кода.

Учтите что показ произвольного содержимого от недоверенных источников влечет за собой риски безопасности, которые Электрон не предназначен купировать. К сведению, наиболее популярные приложения Электрон (Atom, Slack, Visual Studio Code, etc) предназначены для локальной работы (или с доверенными удаленными узлами, без Node интеграции). Если ваше приложение выполняет код из онлайн источника, вы обязаны обеспечить безопасность кода.

## Отчеты по безопасности

Для получения информации о том, как правильно устранять уязвимости Electron, загляни в [SECURITY.md](https://github.com/electron/electron/tree/master/SECURITY.md)

## Вопросы и обновления безопасности Chromium

Electron keeps up to date with alternating Chromium releases. For more information, see the [Electron Release Cadence blog post](https://electronjs.org/blog/12-week-cadence).

## Безопасность - это ответственность каждого

Важно помнить, что безопасность вашего Electron приложения является результатом общей безопасности основы платформы (*Chromium*, *Node.js*), самого Electron, всех NPM-зависимостей и вашего кода. Поэтому вы обязаны следовать нескольким важным рекомендациям:

* **Keep your application up-to-date with the latest Electron framework release.** When releasing your product, you’re also shipping a bundle composed of Electron, Chromium shared library and Node.js. Vulnerabilities affecting these components may impact the security of your application. By updating Electron to the latest version, you ensure that critical vulnerabilities (such as *nodeIntegration bypasses*) are already patched and cannot be exploited in your application. For more information, see "[Use a current version of Electron](#16-use-a-current-version-of-electron)".

* **Оцените свои зависимости.** В то время как NPM предоставляет полмиллиона многоразовых пакетов, вы несете ответственность за выбор надежных библиотек третьей стороны. If you use outdated libraries affected by known vulnerabilities or rely on poorly maintained code, your application security could be in jeopardy.

* **Используйте безопасные методы программирования** Первая линия защиты вашей заявки — ваш собственный код. Common web vulnerabilities, such as Cross-Site Scripting (XSS), have a higher security impact on Electron applications hence it is highly recommended to adopt secure software development best practices and perform security testing.

## Изоляция ненадежного контента

Проблемы безопасности возникают всякий раз, когда вы получаете код из ненадежного источника (напр., удаленный сервер) и выполняете его локально. As an example, consider a remote website being displayed inside a default [`BrowserWindow`][browser-window]. If an attacker somehow manages to change said content (either by attacking the source directly, or by sitting between your app and the actual destination), they will be able to execute native code on the user's machine.

> :warning:  ни при каких обстоятельствах не следует загружать и выполнять удаленный код с Node.js integration enabled. Вместо этого используйте только локальные файлы (упакованные вместе с вашим приложением), для выполнения Node.js кода. To display remote content, use the [`<webview>`][webview-tag] tag or [`BrowserView`][browser-view], make sure to disable the `nodeIntegration` and enable `contextIsolation`.

## Предупреждение безопасности Electron

Электрон 2.0 показывает разработчикам предупреждения и рекомендации в консоли. Они показываются только тогда, когда имя исполняемого файла - Electron.

Вы можете принудительно включить или выключить эти предупреждения в настройках `ELECTRON_ENABLE_SECURITY_WARNINGS` или `ELECTRON_DISABLE_SECURITY_WARNINGS` на любом `process.env` или объекте `window`.

## Чеклист: рекомендации по безопасности

You should at least follow these steps to improve the security of your application:

1. [Загружайте только безопасный контент](#1-only-load-secure-content)
2. [Выключите Node.js интеграцию во всех видах (renderers) показывающих удаленный контент](#2-do-not-enable-nodejs-integration-for-remote-content)
3. [Включите контекстную изоляцию для всех видов (renders) с удаленным контентом](#3-enable-context-isolation-for-remote-content)
4. [Enable sandboxing](#4-enable-sandboxing)
5. [Используйте `ses.setPermissionRequestHandler()` в сессиях с загрузкой удаленного контента](#5-handle-session-permission-requests-from-remote-content)
6. [Не выключайте `webSecurity`](#6-do-not-disable-websecurity)
7. [Определите `Content-Security-Policy`](#7-define-a-content-security-policy) и используйте ограничительные правила (i.e. `script-src 'self'`)
8. [Не устанавливайте `allowRunningInsecureContent` в `true`](#8-do-not-set-allowrunninginsecurecontent-to-true)
9. [Не включайте экспериментальные функции](#9-do-not-enable-experimental-features)
10. [Не испольхуйте `enableBlinkFeatures`](#10-do-not-use-enableblinkfeatures)
11. [`<webview>`: Do not use `allowpopups`](#11-do-not-use-allowpopups)
12. [`<webview>`: Verify options and params](#12-verify-webview-options-before-creation)
13. [Disable or limit navigation](#13-disable-or-limit-navigation)
14. [Disable or limit creation of new windows](#14-disable-or-limit-creation-of-new-windows)
15. [Do not use `openExternal` with untrusted content](#15-do-not-use-openexternal-with-untrusted-content)
16. [Use a current version of Electron](#16-use-a-current-version-of-electron)

To automate the detection of misconfigurations and insecure patterns, it is possible to use [electronegativity](https://github.com/doyensec/electronegativity). For additional details on potential weaknesses and implementation bugs when developing applications using Electron, please refer to this [guide for developers and auditors](https://doyensec.com/resources/us-17-Carettoni-Electronegativity-A-Study-Of-Electron-Security-wp.pdf)

## 1) Загружать только безопасный контент

Все ресурсы не включенные в ваше приложение должны быть загружены с использованием безопасного протокола `HTTPS`. Откажитесь от не безопасных протоколов, таких как `HTTP`. Так же мы рекомендуем `WSS` over `WS`, `FTPS` over `FTP`, и т. п.

### Почему?

`HTTPS` имеет 3 главных преимущества:

1) It authenticates the remote server, ensuring your app connects to the correct host instead of an impersonator. 2) It ensures data integrity, asserting that the data was not modified while in transit between your application and the host. 3) Он шифрует трафик между вашим пользователем и хостом назначения, что усложняет перехват информации, отправленную между вашим приложением и хостом.

### Как?

```js
// Плохо
browserWindow.loadURL('http://example.com') 

// Хорошо
browserWindow.loadURL('https://example.com')
```

```html
<!-- Плохо -->
<script crossorigin src="http://example.com/react.js"></script>
<link rel="stylesheet" href="http://example.com/style.css">

<!-- Хорошо -->
<script crossorigin src="https://example.com/react.js"></script>
<link rel="stylesheet" href="https://example.com/style.css">
```

## 2) Do not enable Node.js Integration for Remote Content

_This recommendation is the default behavior in Electron since 5.0.0._

It is paramount that you do not enable Node.js integration in any renderer ([`BrowserWindow`][browser-window], [`BrowserView`][browser-view], or [`<webview>`][webview-tag]) that loads remote content. The goal is to limit the powers you grant to remote content, thus making it dramatically more difficult for an attacker to harm your users should they gain the ability to execute JavaScript on your website.

After this, you can grant additional permissions for specific hosts. For example, if you are opening a BrowserWindow pointed at `https://example.com/`, you can give that website exactly the abilities it needs, but no more.

### Почему?

A cross-site-scripting (XSS) attack is more dangerous if an attacker can jump out of the renderer process and execute code on the user's computer. Cross-site-scripting attacks are fairly common - and while an issue, their power is usually limited to messing with the website that they are executed on. Disabling Node.js integration helps prevent an XSS from being escalated into a so-called "Remote Code Execution" (RCE) attack.

### Как?

```js
// Bad
const mainWindow = new BrowserWindow({
  webPreferences: {
    nodeIntegration: true,
    nodeIntegrationInWorker: true
  }
})

mainWindow.loadURL('https://example.com')
```

```js
// Good
const mainWindow = new BrowserWindow({
  webPreferences: {
    preload: path.join(app.getAppPath(), 'preload.js')
  }
})

mainWindow.loadURL('https://example.com')
```

```html
<!-- Bad -->
<webview nodeIntegration src="page.html"></webview>

<!-- Good -->
<webview src="page.html"></webview>
```

При отключении интеграции с Node.js, можно по-прежнему использовать API на вашем сайте, которые используют модули или функции Node.js. Preload scripts continue to have access to `require` and other Node.js features, allowing developers to expose a custom API to remotely loaded content.

In the following example preload script, the later loaded website will have access to a `window.readConfig()` method, but no Node.js features.

```js
const { readFileSync } = require('fs')

window.readConfig = function () {
  const data = readFileSync('./config.json')
  return data
}
```

## 3) Включите контекстную изоляцию для удаленного содержимого

Context isolation is an Electron feature that allows developers to run code in preload scripts and in Electron APIs in a dedicated JavaScript context. In practice, that means that global objects like `Array.prototype.push` or `JSON.parse` cannot be modified by scripts running in the renderer process.

Electron uses the same technology as Chromium's [Content Scripts](https://developer.chrome.com/extensions/content_scripts#execution-environment) to enable this behavior.

Even when `nodeIntegration: false` is used, to truly enforce strong isolation and prevent the use of Node primitives `contextIsolation` **must** also be used.

### Why & How?

For more information on what `contextIsolation` is and how to enable it please see our dedicated [Context Isolation](context-isolation.md) document.

## 4) Enable Sandboxing

[Sandboxing](sandbox.md) is a Chromium feature that uses the operating system to significantly limit what renderer processes have access to. You should enable the sandbox in all renderers. Loading, reading or processing any untrusted content in an unsandboxed process, including the main process, is not advised.

### Как?

When creating a window, pass the `sandbox: true` option in `webPreferences`:

```js
const win = new BrowserWindow({
  webPreferences: {
    sandbox: true
  }
})
```

## 5) Handle Session Permission Requests From Remote Content

You may have seen permission requests while using Chrome: They pop up whenever the website attempts to use a feature that the user has to manually approve ( like notifications).

The API is based on the [Chromium permissions API](https://developer.chrome.com/extensions/permissions) and implements the same types of permissions.

### Почему?

By default, Electron will automatically approve all permission requests unless the developer has manually configured a custom handler. While a solid default, security-conscious developers might want to assume the very opposite.

### Как?

```js
const { session } = require('electron')

session
  .fromPartition('some-partition')
  .setPermissionRequestHandler((webContents, permission, callback) => {
    const url = webContents.getURL()

    if (permission === 'notifications') {
      // Approves the permissions request
      callback(true)
    }

    // Verify URL
    if (!url.startsWith('https://example.com/')) {
      // Denies the permissions request
      return callback(false)
    }
  })
```

## 6) Do Not Disable WebSecurity

_Recommendation is Electron's default_

You may have already guessed that disabling the `webSecurity` property on a renderer process ([`BrowserWindow`][browser-window], [`BrowserView`][browser-view], or [`&lt;webview&gt;`][webview-tag]) disables crucial security features.

Do not disable `webSecurity` in production applications.

### Почему?

Disabling `webSecurity` will disable the same-origin policy and set `allowRunningInsecureContent` property to `true`. In other words, it allows the execution of insecure code from different domains.

### Как?

```js
// Bad
const mainWindow = new BrowserWindow({
  webPreferences: {
    webSecurity: false
  }
})
```

```js
// Good
const mainWindow = new BrowserWindow()
```

```html
<!-- Bad -->
<webview disablewebsecurity src="page.html"></webview>

<!-- Good -->
<webview src="page.html"></webview>
```

## 7) Define a Content Security Policy

A Content Security Policy (CSP) is an additional layer of protection against cross-site-scripting attacks and data injection attacks. We recommend that they be enabled by any website you load inside Electron.

### Почему?

CSP allows the server serving content to restrict and control the resources Electron can load for that given web page. `https://example.com` should be allowed to load scripts from the origins you defined while scripts from `https://evil.attacker.com` should not be allowed to run. Defining a CSP is an easy way to improve your application's security.

The following CSP will allow Electron to execute scripts from the current website and from `apis.example.com`.

```plaintext
// Bad
Content-Security-Policy: '*'

// Good
Content-Security-Policy: script-src 'self' https://apis.example.com
```

### CSP HTTP Header

Electron respects the [`Content-Security-Policy` HTTP header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy) which can be set using Electron's [`webRequest.onHeadersReceived`](../api/web-request.md#webrequestonheadersreceivedfilter-listener) handler:

```javascript
const { session } = require('electron')

session.defaultSession.webRequest.onHeadersReceived((details, callback) => {
  callback({
    responseHeaders: {
      ...details.responseHeaders,
      'Content-Security-Policy': ['default-src \'none\'']
    }
  })
})
```

### CSP Meta Tag

CSP's preferred delivery mechanism is an HTTP header, however it is not possible to use this method when loading a resource using the `file://` protocol. It can be useful in some cases, such as using the `file://` protocol, to set a policy on a page directly in the markup using a `&lt;meta&gt;` tag:

```html
<meta http-equiv="Content-Security-Policy" content="default-src 'none'">
```

## 8) Do Not Set `allowRunningInsecureContent` to `true`

_Recommendation is Electron's default_

By default, Electron will not allow websites loaded over `HTTPS` to load and execute scripts, CSS, or plugins from insecure sources (`HTTP`). Setting the property `allowRunningInsecureContent` to `true` disables that protection.

Loading the initial HTML of a website over `HTTPS` and attempting to load subsequent resources via `HTTP` is also known as "mixed content".

### Почему?

Loading content over `HTTPS` assures the authenticity and integrity of the loaded resources while encrypting the traffic itself. See the section on [only displaying secure content](#1-only-load-secure-content) for more details.

### Как?

```js
// Bad
const mainWindow = new BrowserWindow({
  webPreferences: {
    allowRunningInsecureContent: true
  }
})
```

```js
// Good
const mainWindow = new BrowserWindow({})
```

## 9) Do Not Enable Experimental Features

_Recommendation is Electron's default_

Advanced users of Electron can enable experimental Chromium features using the `experimentalFeatures` property.

### Почему?

Experimental features are, as the name suggests, experimental and have not been enabled for all Chromium users. Furthermore, their impact on Electron as a whole has likely not been tested.

Legitimate use cases exist, but unless you know what you are doing, you should not enable this property.

### Как?

```js
// Bad
const mainWindow = new BrowserWindow({
  webPreferences: {
    experimentalFeatures: true
  }
})
```

```js
// Good
const mainWindow = new BrowserWindow({})
```

## 10) Do Not Use `enableBlinkFeatures`

_Recommendation is Electron's default_

Blink is the name of the rendering engine behind Chromium. As with `experimentalFeatures`, the `enableBlinkFeatures` property allows developers to enable features that have been disabled by default.

### Почему?

Generally speaking, there are likely good reasons if a feature was not enabled by default. Legitimate use cases for enabling specific features exist. As a developer, you should know exactly why you need to enable a feature, what the ramifications are, and how it impacts the security of your application. Under no circumstances should you enable features speculatively.

### Как?

```js
// Bad
const mainWindow = new BrowserWindow({
  webPreferences: {
    enableBlinkFeatures: 'ExecCommandInJavaScript'
  }
})
```

```js
// Good
const mainWindow = new BrowserWindow()
```

## 11) Do Not Use `allowpopups`

_Recommendation is Electron's default_

If you are using [`<webview>`][webview-tag], you might need the pages and scripts loaded in your `<webview>` tag to open new windows. The `allowpopups` attribute enables them to create new [`BrowserWindows`][browser-window] using the `window.open()` method. `<webview>` tags are otherwise not allowed to create new windows.

### Почему?

If you do not need popups, you are better off not allowing the creation of new [`BrowserWindows`][browser-window] by default. This follows the principle of minimally required access: Don't let a website create new popups unless you know it needs that feature.

### Как?

```html
<!-- Bad -->
<webview allowpopups src="page.html"></webview>

<!-- Good -->
<webview src="page.html"></webview>
```

## 12) Verify WebView Options Before Creation

A WebView created in a renderer process that does not have Node.js integration enabled will not be able to enable integration itself. However, a WebView will always create an independent renderer process with its own `webPreferences`.

It is a good idea to control the creation of new [`<webview>`][webview-tag] tags from the main process and to verify that their webPreferences do not disable security features.

### Почему?

Since `<webview>` live in the DOM, they can be created by a script running on your website even if Node.js integration is otherwise disabled.

Electron enables developers to disable various security features that control a renderer process. In most cases, developers do not need to disable any of those features - and you should therefore not allow different configurations for newly created [`<webview>`][webview-tag] tags.

### Как?

Before a [`<webview>`][webview-tag] tag is attached, Electron will fire the `will-attach-webview` event on the hosting `webContents`. Use the event to prevent the creation of `webViews` with possibly insecure options.

```js
app.on('web-contents-created', (event, contents) => {
  contents.on('will-attach-webview', (event, webPreferences, params) => {
    // Strip away preload scripts if unused or verify their location is legitimate
    delete webPreferences.preload
    delete webPreferences.preloadURL

    // Disable Node.js integration
    webPreferences.nodeIntegration = false

    // Verify URL being loaded
    if (!params.src.startsWith('https://example.com/')) {
      event.preventDefault()
    }
  })
})
```

Again, this list merely minimizes the risk, it does not remove it. If your goal is to display a website, a browser will be a more secure option.

## 13) Disable or limit navigation

If your app has no need to navigate or only needs to navigate to known pages, it is a good idea to limit navigation outright to that known scope, disallowing any other kinds of navigation.

### Почему?

Navigation is a common attack vector. If an attacker can convince your app to navigate away from its current page, they can possibly force your app to open web sites on the Internet. Even if your `webContents` are configured to be more secure (like having `nodeIntegration` disabled or `contextIsolation` enabled), getting your app to open a random web site will make the work of exploiting your app a lot easier.

A common attack pattern is that the attacker convinces your app's users to interact with the app in such a way that it navigates to one of the attacker's pages. This is usually done via links, plugins, or other user-generated content.

### Как?

If your app has no need for navigation, you can call `event.preventDefault()` in a [`will-navigate`][will-navigate] handler. If you know which pages your app might navigate to, check the URL in the event handler and only let navigation occur if it matches the URLs you're expecting.

We recommend that you use Node's parser for URLs. Simple string comparisons can sometimes be fooled - a `startsWith('https://example.com')` test would let `https://example.com.attacker.com` through.

```js
const URL = require('url').URL

app.on('web-contents-created', (event, contents) => {
  contents.on('will-navigate', (event, navigationUrl) => {
    const parsedUrl = new URL(navigationUrl)

    if (parsedUrl.origin !== 'https://example.com') {
      event.preventDefault()
    }
  })
})
```

## 14) Disable or limit creation of new windows

If you have a known set of windows, it's a good idea to limit the creation of additional windows in your app.

### Почему?

Much like navigation, the creation of new `webContents` is a common attack vector. Attackers attempt to convince your app to create new windows, frames, or other renderer processes with more privileges than they had before; or with pages opened that they couldn't open before.

If you have no need to create windows in addition to the ones you know you'll need to create, disabling the creation buys you a little bit of extra security at no cost. This is commonly the case for apps that open one `BrowserWindow` and do not need to open an arbitrary number of additional windows at runtime.

### Как?

[`webContents`][web-contents] will delegate to its [window open handler][window-open-handler] before creating new windows. The handler will receive, amongst other parameters, the `url` the window was requested to open and the options used to create it. We recommend that you register a handler to monitor the creation of windows, and deny any unexpected window creation.

```js
const { shell } = require('electron')

app.on('web-contents-created', (event, contents) => {
  contents.setWindowOpenHandler(({ url }) => {
    // In this example, we'll ask the operating system
    // to open this event's url in the default browser.
    //
    // See the following item for considerations regarding what
    // URLs should be allowed through to shell.openExternal.
    if (isSafeForExternalOpen(url)) {
      setImmediate(() => {
        shell.openExternal(url)
      })
    }

    return { action: 'deny' }
  })
})
```

## 15) Do not use `openExternal` with untrusted content

Shell's [`openExternal`][open-external] allows opening a given protocol URI with the desktop's native utilities. On macOS, for instance, this function is similar to the `open` terminal command utility and will open the specific application based on the URI and filetype association.

### Почему?

Improper use of [`openExternal`][open-external] can be leveraged to compromise the user's host. When openExternal is used with untrusted content, it can be leveraged to execute arbitrary commands.

### Как?

```js
//  Bad
const { shell } = require('electron')
shell.openExternal(USER_CONTROLLED_DATA_HERE)
```

```js
//  Good
const { shell } = require('electron')
shell.openExternal('https://example.com/index.html')
```

## 16) Use a current version of Electron

You should strive for always using the latest available version of Electron. Whenever a new major version is released, you should attempt to update your app as quickly as possible.

### Почему?

An application built with an older version of Electron, Chromium, and Node.js is an easier target than an application that is using more recent versions of those components. Generally speaking, security issues and exploits for older versions of Chromium and Node.js are more widely available.

Both Chromium and Node.js are impressive feats of engineering built by thousands of talented developers. Given their popularity, their security is carefully tested and analyzed by equally skilled security researchers. Many of those researchers [disclose vulnerabilities responsibly][responsible-disclosure], which generally means that researchers will give Chromium and Node.js some time to fix issues before publishing them. Your application will be more secure if it is running a recent version of Electron (and thus, Chromium and Node.js) for which potential security issues are not as widely known.

[browser-window]: ../api/browser-window.md

[browser-window]: ../api/browser-window.md
[browser-view]: ../api/browser-view.md
[webview-tag]: ../api/webview-tag.md
[webview-tag]: ../api/webview-tag.md
[web-contents]: ../api/web-contents.md
[window-open-handler]: ../api/web-contents.md#contentssetwindowopenhandlerhandler
[will-navigate]: ../api/web-contents.md#event-will-navigate
[open-external]: ../api/shell.md#shellopenexternalurl-options
[responsible-disclosure]: https://en.wikipedia.org/wiki/Responsible_disclosure
