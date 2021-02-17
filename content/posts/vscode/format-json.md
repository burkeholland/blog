---
title: "How to format JSON with VS Code"
date: 2021-02-17T10:03:14-06:00
draft: true
---

VS Code highlights, but doesn't format JSON by default. You'll need to install the [Prettier Extension](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode&WT.mc_id=devcloud-16897-buhollan) to get formatting for JSON.

## Using Prettier to format JSON

The [Prettier Extension](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode&WT.mc_id=devcloud-16897-buhollan) for VS Code will solve virtually all of your JavaScript formatting woes, including JSON.

Once installed, just select "Format Document" from the Command Palette and go from this...

![json in VS Code wrapping onto multiple lines](/media/vscode/format-json/vscode-json-word-wrap.jpg)

To this...

![](/media/vscode/format-json/vscode-json-formatted.jpg)

I'd also recommend turning on "Format On Save" in your settings file. This will format any file as soon as you save it.

```json
"editor.formatOnSave": true 
```

Finally, consider installing the [Formating Toggle extension](https://marketplace.visualstudio.com/items?itemName=tombonnike.vscode-status-bar-format-toggle&WT.mc_id=devcloud-16897-buhollan) which allows you to quickly turn "Format On Save" off and on for those times when you don't want formatting, or Prettier formatting is conflicting with the formatting of a file and making you sad.


