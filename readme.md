# Learning Electron

## Understanding Processes
- There are two types of processes:
    - main (entry point)
    - renderer

## 1) main process
- Each app has one main process, acts as an entry point for the app
- Works with 2 modules: BrowserWindow, app
#### BrowserWindow module
- Purpose: create and manage application windows
- Runs in Node.js environment and thus have the ability to ~require~ modules and use all of Node.js APIs
- Each instance of BrowserWindow loads a web page in a separate renderer process
- To interact with the web content from the main process, you can use the window's webContents object as shown below
```
const { BrowserWindow } = require('electron')

const win = new BrowserWindow({ width: 800, height: 1500 })
win.loadURL('https://github.com')

const contents = win.webContents
console.log(contents)
```
- BrowserWindow module is an EventEmitter. You can add handlers for various user events (minimizing, maximizing windows)
- When a BrowserWindow instance is destroyed -> renderer process terminated as well
#### app module
- Purpose: control application's lifecycle
- Provides large set of events and methods


## 2) renderer process
- A renderer is responsible for rendering web content
- A HTML file is entry point for renderer process
- UI styling is added through CSS
- Executable Javascript code can be added through ``<script>`` elements
- renderer has no direct access to require or other Node.js APIs


### Preload scripts
- contains codes that executes in a renderer process before its web content begins loading
- these scripts run winthin the renderer context but are granted more privileges by having access to Node.js APIs
- Below shows how you can attach a preload script:
```
const { BrowserWindow } = require('electron')
const win = new BrowserWindow({
  webPreferences: {
    preload: 'path/to/preload.js',
  },
})
```
- preload scripts shares a global ``window`` global with the renderer they are attached to. below is how you can make use of this
    - preload.js
        ```
        const { contextBridge  } = require('electron')

        contextBridge.exposeInMainWorld('myAPI', {
            desktop: true,
        })
        ```
    - render.js
        ```
        console.log(window.myAPI)
        // => { desktop: true }
        ```




