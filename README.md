This repo should reproduce [NX Issue 22358](https://github.com/nrwl/nx/issues/22358).

```
D:\repro-nx-win [main ≡]> npx nx report

 NX   Report complete - copy this into the issue template

Node   : 20.12.2
OS     : win32-x64
npm    : 9.5.0

nx             : 19.0.3
@nx/js         : 19.0.3
@nx/workspace  : 19.0.3
@nx/devkit     : 19.0.3
@nx/esbuild    : 19.0.3
@nrwl/tao      : 19.0.3
typescript     : 5.4.5
```

Working without Windows PTY:
```
D:\repro-nx-win [main ≡ +1 ~0 -0 !]> ${env:NX_WINDOWS_PTY_SUPPORT}='true'
D:\repro-nx-win [main ≡ +1 ~0 -0 !]> ${env:NX_NATIVE_LOGGING}='nx::native::pseudo_terminal'
D:\repro-nx-win [main ≡ +1 ~0 -0 !]> npx nx run --skip-nx-cache app:serve

> nx run app:serve

 NX   Running target build for project app and 1 task it depends on:
> nx run libA:build
Compiling TypeScript files for project "libA"...
Done compiling TypeScript files for project "libA".
> nx run app:build:development
 NX   Successfully ran target build for project app and 1 task it depends on
Debugger listening on ws://localhost:9229/9f61f872-09fc-462a-93d9-5332229115a5
Debugger listening on ws://localhost:9229/9f61f872-09fc-462a-93d9-5332229115a5
For help, see: https://nodejs.org/en/docs/inspector

Hello world!
 NX  Process exited with code 0, waiting for changes to restart...
```

Reproducing with Windows PTY (Process is stuck):
```
D:\repro-nx-win [main ≡ +1 ~0 -0 !]> ${env:NX_WINDOWS_PTY_SUPPORT}='true'
D:\repro-nx-win [main ≡ +1 ~0 -0 !]> ${env:NX_NATIVE_LOGGING}='nx::native::pseudo_terminal'
D:\repro-nx-win [main ≡ +1 ~0 -0 !]> npx nx run --skip-nx-cache app:serve
TRACE log: Opening Pseudo Terminal    
TRACE log: Passing through stdin
TRACE log: Enabling raw mode
TRACE log: Disabling raw mode

> nx run app:serve

TRACE nx::native::pseudo_terminal::rust_pseudo_terminal: nx_fork command: node D:\repro-nx-win\node_modules\nx\src\tasks-runner\fork.js \\.\pipe\nx\C:\Users\tomsm\AppData\Local\Temp\da1f9ff7cb3a40db3cd6\fp25720.sock app:serve
TRACE log: Running node D:\repro-nx-win\node_modules\nx\src\tasks-runner\fork.js \\.\pipe\nx\C:\Users\tomsm\AppData\Local\Temp\da1f9ff7cb3a40db3cd6\fp25720.sock app:serve
TRACE log: Enabling raw mode
TRACE log: Getting running clone
TRACE log: Getting printing_rx clone
TRACE log: spawning thread to wait for command
TRACE log: Returning ChildProcess
TRACE log: Waiting for node D:\repro-nx-win\node_modules\nx\src\tasks-runner\fork.js \\.\pipe\nx\C:\Users\tomsm\AppData\Local\Temp\da1f9ff7cb3a40db3cd6\fp25720.sock app:serve
TRACE log: Quiet: false
TRACE log: Prevented terminal escape sequence ESC[6n from being printed.
```