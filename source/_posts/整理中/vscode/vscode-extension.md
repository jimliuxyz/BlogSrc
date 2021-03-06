---
title: __
---


### create project

```
npm install -g yo generator-code
yo code

```

### publish extension

```
npm install -g vsce

//申請VSTS(Visual Studio Team Services)帳號, then create token
https://go.microsoft.com/fwlink/?LinkId=307137

//create publisher by token
vsce create-publisher jimliuxyz

vsce login jimliuxyz

vsce publish 1.0.0
vsce publish major
vsce publish minor
vsce publish patch
vsce publish

```

[publish-extension](https://code.visualstudio.com/docs/extensions/publish-extension)

[Semantic Versioning](http://semver.org/lang/zh-TW/)
### devlopment

直接點選側欄選單的debug, start debug

### package.json

```json
  "icon": "assert/logo.png",
  "activationEvents": [
    "*"
  ],
  "contributes": {
    "configuration": {
      "type": "object",
      "title": "Word Voice configuration",
      "properties": {
        "wovo.voice_filename": {
          "type": "boolean",
          "default": true,
          "description": "voice filename when changing editor."
        },
        "wovo.voice_editing": {
          "type": "boolean",
          "default": true,
          "description": "voice word when editing."
        },
        "wovo.voice_cursor": {
          "type": "boolean",
          "default": true,
          "description": "voice filename when moving cursor."
        }
      }
    }
  },
```


```js
import {window, commands, Disposable, ExtensionContext, StatusBarAlignment, StatusBarItem, TextDocument} from 'vscode';
import * as vscode from 'vscode';
import * as say from 'say';

const cfgVoFilename = (): boolean =>
vscode.workspace.getConfiguration('wovo').get<boolean>('voice_filename');

const cfgVoEditing = (): boolean =>
vscode.workspace.getConfiguration('wovo').get<boolean>('voice_editing');

const cfgVoCursor = (): boolean =>
vscode.workspace.getConfiguration('wovo').get<boolean>('voice_cursor');

export function activate(context: ExtensionContext) {

    console.log('"WoVo" is now active!');

    // create a new word voice
    let wordVoice = new WordVoice();
    let controller = new WordVoiceController(wordVoice);

    // Add to a list of disposables which are disposed when this extension is deactivated.
    context.subscriptions.push(controller);
    context.subscriptions.push(wordVoice);

    console.log(cfgVoFilename())
}


class WordVoice {

    private WORDRE = /[A-Za-z0-9_]+/;
    private _statusBarItem: StatusBarItem;
    private _prevFilename: string;
    private _prevVersion: number;
    private _delaycall:NodeJS.Timer;
    
    public onDidChnage() {
        
        // Create as needed
        if (!this._statusBarItem) {
            this._statusBarItem = window.createStatusBarItem(StatusBarAlignment.Left);
        }

        // Get the current text editor
        let editor = window.activeTextEditor;
        if (!editor) {
            this._statusBarItem.hide();
            return;
        }
        let doc = editor.document;
        
        // this._statusBarItem.text = doc.fileName+" : "+doc.version;
        // this._statusBarItem.show();
        // this._statusBarItem.hide();
        
        //changing active editor        
        if (doc.fileName !== this._prevFilename) {
            var fname = doc.fileName.replace(/^.*[\\\/]/, "").replace(/[^A-Za-z0-9]/g,". ")/*.replace(/[.].*$/,"")*/;
            //to avoid stopped by cursor voice, so it should voice later
            setTimeout(() => {
                say.stop() //stop cursor voice
                if (cfgVoFilename())
                    say.speak(fname)
            },1)

            this._prevFilename = doc.fileName;
            this._prevVersion = doc.version;
            return;
        }

        var editing = this._prevVersion !== doc.version;
        this._prevVersion = doc.version;
        
        var stext:string;
        if (editor.selection.isEmpty) {
            var pos = editor.selection.active;
            if (pos.line == 0 && pos.character == 0)
                return;    
            
            var char = '\n';
            if (pos.character > 0) {
                char = (doc.lineAt(pos.line).text)[pos.character - 1]
            }

            //on editing
            if (editing) {
                if (!cfgVoEditing())
                    return;
                //move back one character if cursor not in a word
                if (!this.WORDRE.test(char)) {
                    pos = new vscode.Position(pos.line + (pos.character == 0 ? -1 : 0), pos.character > 0 ? (pos.character - 1) : (doc.lineAt(pos.line - 1).text.length))
                }
                //delay if editing a word
                else {
                    clearTimeout(this._delaycall);
                    this._delaycall = setTimeout(() => this.onDidChnage(), 500);
                    return;
                }
            }
            else if (!cfgVoCursor())
                return;    

            var range = doc.getWordRangeAtPosition(pos,this.WORDRE);
            if (range) {
                stext = doc.getText(range);
                // vscode.window.showInformationMessage(stext)
            }
        }

        //split text if has both lower and upper case
        if (stext && (stext !== stext.toLowerCase() && stext !== stext.toUpperCase())) {
            var arr: string[]=[];
            var word = "";
            for (var s of <any>stext) {
                if (s === s.toUpperCase()) {
                    arr.push(word);
                    word = "";                    
                }
                word += s;
            }
            arr.push(word);
            stext = arr.join(" ");
        }

        if (stext) {     
            say.stop()    
            say.speak(stext)
        }
    }

    dispose() {
        this._statusBarItem.dispose();
    }
}

class WordVoiceController {

    private _wordVoice: WordVoice;
    private _disposable: Disposable;

    constructor(wordVoice: WordVoice) {
        this._wordVoice = wordVoice;

        // subscribe to selection change and editor activation events
        let subscriptions: Disposable[] = [];
        window.onDidChangeActiveTextEditor(this._onEvent, this, subscriptions);
        window.onDidChangeTextEditorSelection(this._onEvent, this, subscriptions);

        // create a combined disposable from both event subscriptions
        this._disposable = Disposable.from(...subscriptions);
    }

    dispose() {
        this._disposable.dispose();
    }

    private _onEvent() {
        this._wordVoice.onDidChnage();
    }
}

```