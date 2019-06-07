# Slack Black Theme

A darker, more contrasty, Slack theme.

Original github : https://mallowigi.github.io/slack-one-dark-theme/

# Preview

![Screenshot](https://cloud.githubusercontent.com/assets/7691630/24120350/4cbb643e-0d82-11e7-8353-5d4eb65dfd6a.png)

# Installing into Slack

Find your Slack's application directory.

* Windows: `%homepath%\AppData\Local\slack\`
* Mac: `/Applications/Slack.app/Contents/`
* Linux: `/usr/lib/slack/` (Debian-based)


Open up the most recent version (e.g. `app-2.5.1`) then open
`resources\app.asar.unpacked\src\static\index.js`

For versions after and including `3.0.0` the same code must be added to the following file
`resources\app.asar.unpacked\src\static\ssb-interop.js`

At the very bottom, add

```js
// First make sure the wrapper app is loaded
document.addEventListener("DOMContentLoaded", function() {

  // Then get its webviews
  let webviews = document.querySelectorAll(".TeamView webview");

  // Fetch our CSS in parallel ahead of time
  const cssPath = 'https://raw.githubusercontent.com/bencalldev/Slack-custom/master/custom.css';
  let cssPromise = fetch(cssPath).then(response => response.text());

  let customCustomCSS = `
  :root {
     /* Modify these to change your theme colors: */
    --primary: #09F;
    --text: #CCC;
    --background: #080808;
    --background-elevated: #222;
    --accent: #568AF2;

    /* These should be less important: */
    --background-hover: lighten(#3B4048, 10%);
    --background-light: #AAA;
    --background-bright: #FFF;

    --border-dim: #666;
    --border-bright: var(--primary);

    --text-bright: #FFF;
    --text-dim: #555c69;
    --text-special: var(--primary);
    --text-accent: var(--text-bright);

    --scrollbar-background: #000;
    --scrollbar-border: var(--primary);

    --yellow: #fc0;
    --green: #98C379;
    --cyan: #56B6C2;
    --blue: #61AFEF;
    --purple: #C678DD;
    --red: #E06C75;
    --red2: #BE5046;
    --orange: #D19A66;
    --orange2: #E5707B;
    --gray: #3E4451;
    --silver: #9da5b4;
    --black: #21252b;
     }
  `

  // Insert a style tag into the wrapper view
  cssPromise.then(css => {
     let s = document.createElement('style');
     s.type = 'text/css';
     s.innerHTML = css + customCustomCSS;
     document.head.appendChild(s);
  });

  // Wait for each webview to load
  webviews.forEach(webview => {
     webview.addEventListener('ipc-message', message => {
        if (message.channel == 'didFinishLoading')
           // Finally add the CSS into the webview
           cssPromise.then(css => {
              let script = `
                    let s = document.createElement('style');
                    s.type = 'text/css';
                    s.id = 'slack-custom-css';
                    s.innerHTML = \`${css + customCustomCSS}\`;
                    document.head.appendChild(s);
                    `
              webview.executeJavaScript(script);
           })
     });
  });
});
```

* Notice that you can edit any of the theme colors using the custom CSS (for
the already-custom theme.)
* Also, you can put any CSS URL you want in `cssPath`,
so you don't necessarily need to create an entire fork to change some small styles.

That's it! Restart Slack and see how well it works.

**NB: You'll have to do this every time Slack updates.**

# Customize css (Windows)

Duplicate (or edit) the start menu shortcut and change the target to

```bash
C:\Windows\System32\cmd.exe /c " SET SLACK_DEVELOPER_MENU=TRUE && start C:\existing\path\to\slack.exe"
```

(*Don't forget to change the exe slack url by yours* : `C:\Users\ExampleUser\AppDate\Local\slack\slack.exe`)

Start Slack with this new shortcut and to open developer console : press `Ctrl` + `Alt` + `I`

# License

Apache 2.0