# Viết ứng dụng Electron đầu tiên của bạn

Electron cung cấp cho bạn khả năng tạo ra ứng dụng desktop với JavaScript thuần bằng cách cung cấp thời gian chạy với một số lượng lớn APIs tự nhiên ( hệ điều hành). Bạn có thể xem nó như một biến thể thời gian chạy chương trình của Node.js mà sẽ tập trung hơn vào các ứng dụng trên desktop thay vì các web servers.

Điều này không có nghĩa là Electron là một ứng dụng chạy JavaScript có ràng buộc với các thư viện giao diện (GUI). Thay vì đó, Electron sử dụng các trang web như chính giao diện của bản thân, vì vậy bạn có thể coi nó như một phiên bản trình duyệt Chromium rút gọn, được điều khiển thông qua JavaScript.

**Ghi chú **: Ví dụ này cũng có sẵn dưới dạng repository nên bạn có thể [tải và chạy ngay lập tức ](#trying-this-example).

Theo như việc phát triển được quan tâm, một ứng dụng Electron bản chất chính là một ứng dụng Node.js. Bắt đầu bằng một file `package.json` là một ví dụ cụ thể rằng đây là một module của Node.js. Một ứng dụng cơ bản nhất của Electron sẽ có những cấu trúc thư mục như sau:

```plaintext
your-app/
├── package.json
├── main.js
└── index.html
```

Tạo một thư mục trống mới cho ứng dụng Electron của bạn. Mở command line client và chạy lệnh `npm init` từ thư mục đã tải.

```sh
npm init
```

Câu lệnh npm sẽ điều hướng bạn bằng cách tạo ra một file `package.json` cơ bản. Script được xách định bằng miền `main` là một script khởi đầu ứng dụng của bạn, và cũng chính là nơi chạy qui trình chính của ứng dụng. Một ví dụ về file `package.json` sẽ giống như thế này:

```json
{
  "name": "your-app",
  "version": "0.1.0",
  "main": "main.js"
}
```

__Ghi chú __: Nếu miền `main` không hiện diện trong file `package.json`, Electron sẽ cố load file `index.js` (như cách mà Node.js thực hiện). Nếu đây thực ra là một ứng dụng Node cơ bản, bạn nên thêm một script `start` để ra lệnh cho `node` thực thi gói hiện hành:

```json
{
  "name": "your-app",
  "version": "0.1.0",
  "main": "main.js",
  "scripts": {
    "start": "node ."
  }
}
```

Việc biến ứng dụng Node này thành một ứng dụng Electron thì khá đơn giản - chúng ta chỉ cần thay thế thời gian thực thi `node` với thời gian thực thi `electron`.

```json
{
  "name": "your-app",
  "version": "0.1.0",
  "main": "main.js",
  "scripts": {
    "start": "electron ."
  }
}
```

## Cài đặt Electron

At this point, you'll need to install `electron` itself. The recommended way of doing so is to install it as a development dependency in your app, which allows you to work on multiple apps with different Electron versions. To do so, run the following command from your app's directory:

```sh
npm install --save-dev electron
```

Other means for installing Electron exist. Please consult the [installation guide](installation.md) to learn about use with proxies, mirrors, and custom caches.

## Tóm tắt phát triển Electron

Electron apps are developed in JavaScript using the same principles and methods found in Node.js development. All APIs and features found in Electron are accessible through the `electron` module, which can be required like any other Node.js module:

```javascript
const electron = require('electron')
```

The `electron` module exposes features in namespaces. As examples, the lifecycle of the application is managed through `electron.app`, windows can be created using the `electron.BrowserWindow` class. A simple `main.js` file might wait for the application to be ready and open a window:

```javascript
const { app, BrowserWindow } = require('electron')

function createWindow () {
  // Create the browser window.
  let win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      nodeIntegration: true
    }
  })

  // and load the index.html of the app.
  win.loadFile('index.html')
}

app.whenReady().then(createWindow)
```

The `main.js` should create windows and handle all the system events your application might encounter. A more complete version of the above example might open developer tools, handle the window being closed, or re-create windows on macOS if the user clicks on the app's icon in the dock.

```javascript
const { app, BrowserWindow } = require('electron')

function createWindow () {
  // Create the browser window.
  const win = new BrowserWindow({
    width: 800,
    height: 600,
    webPreferences: {
      nodeIntegration: true
    }
  })

  // and load the index.html of the app.
  win.loadFile('index.html')

  // Open the DevTools.
  win.webContents.openDevTools()
}

// This method will be called when Electron has finished
// initialization and is ready to create browser windows.
// Some APIs can only be used after this event occurs.
app.whenReady().then(createWindow)

// Quit when all windows are closed.
app.on('window-all-closed', () => {
  // On macOS it is common for applications and their menu bar
  // to stay active until the user quits explicitly with Cmd + Q
  if (process.platform !== 'darwin') {
    app.quit()
  }
})

app.on('activate', () => {
  // On macOS it's common to re-create a window in the app when the
  // dock icon is clicked and there are no other windows open.
  if (BrowserWindow.getAllWindows().length === 0) {
    createWindow()
  }
})

// In this file you can include the rest of your app's specific main process
// code. You can also put them in separate files and require them here.
```

Finally the `index.html` is the web page you want to show:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Hello World!</title>
    <!-- https://electronjs.org/docs/tutorial/security#csp-meta-tag -->
    <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
  </head>
  <body>
    <h1>Hello World!</h1>
    We are using node <script>document.write(process.versions.node)</script>,
    Chrome <script>document.write(process.versions.chrome)</script>,
    and Electron <script>document.write(process.versions.electron)</script>.
  </body>
</html>
```

## Chạy ứng dụng của bạn

Once you've created your initial `main.js`, `index.html`, and `package.json` files, you can try your app by running `npm start` from your application's directory.

## Trying this Example

Clone and run the code in this tutorial by using the [`electron/electron-quick-start`][quick-start] repository.

**Note**: Running this requires [Git](https://git-scm.com) and [npm](https://www.npmjs.com/).

```sh
# Clone the repository
$ git clone https://github.com/electron/electron-quick-start
# Go into the repository
$ cd electron-quick-start
# Install dependencies
$ npm install
# Run the app
$ npm start
```

For a list of boilerplates and tools to kick-start your development process, see the [Boilerplates and CLIs documentation][boilerplates].

[quick-start]: https://github.com/electron/electron-quick-start
[boilerplates]: ./boilerplates-and-clis.md