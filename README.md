# atom-sync package

atom-sync is an Atom package to sync files bidirectionally between remote host and local over ssh+rsync. Inspired by [Sublime SFTP](http://wbond.net/sublime_packages/sftp).

[![experimental](http://badges.github.io/stability-badges/dist/experimental.svg)](http://github.com/badges/stability-badges)[![Build Status](https://travis-ci.org/dingjie/atom-sync.svg?branch=master)](https://travis-ci.org/dingjie/atom-sync)

> This package is currently in early development and has only been tested on Mac. Please kindly [try it out](http://atom.io/packages/atom-sync) and [provide feedback](https://github.com/dingjie/atom-sync/issues/new).

![atom-sync](https://cloud.githubusercontent.com/assets/586262/8085519/2b63a7c4-0fc3-11e5-930a-685b09fe7af3.gif)

### Feature ###
* Sync over ssh+rsync — still [secure](http://www.sakana.fr/blog/2008/05/07/securing-automated-rsync-over-ssh/), but much [faster](http://stackoverflow.com/questions/20244585/what-is-the-difference-between-scp-and-rsync).
* [Multi-Folder Projects](http://blog.atom.io/2015/04/15/multi-folder-projects.html) with different sync config files supported
* Triggers conditionally run commands after successful uploading

### Prerequisite ###
* Ensure you have `ssh` and `rsync` installed.

### Quick Start ###
* Open a project folder to sync in [Atom](http://atom.io).
* Right click on the project folder and select `Sync` -> `Edit Remote Config`.
* Edit and save the config file.
* Right click on the project folder and select `Sync` -> `Sync Remote -> Local`.
* Watch water flows.

### Notice ###
* You have to [assign your key file](https://www.linode.com/docs/security/use-public-key-authentication-with-ssh) and better host name in .ssh/config in advanced. Try to [Simplify Your Life With an SSH Config File](http://nerderati.com/2011/03/17/simplify-your-life-with-an-ssh-config-file/).
* For password based login you must install [sshpass](https://gist.github.com/arunoda/7790979) and set the `shell` property to  `/usr/bin/sshpass -p password ssh -o StrictHostKeyChecking=no -l username ` in the configuration file.

### Config File (and Tutorial) ###
> .sync-config.cson

```
remote:
    host: "HOSTNAME",       # server name or ip or ssh host abbr in .ssh/config
    user: "USERNAME",       # ssh username
    path: "REMOTE_DIR"      # e.g. /home/someone/somewhere

behaviour:
    uploadOnSave: true      # Upload every time you save a file
    syncDownOnOpen: true    # Download every time you open a file
    forgetConsole: false    # Never show console panel even while syncing
    autoHideConsole: true   # Hide console automatically after 1.5s
    alwaysSyncAll: false    # Sync all files and folders under the project \
                            # instead of syncing single file or folder
option:
    deleteFiles: true       # Delete files during syncing
    autoHideDelay: 1500     # Time delay to hide console
    exclude: [              # Excluding patterns
        '.sync-config.cson'
        '.git'
        'node_modules'
        'tmp'
        'vendor'
    ]
    flags: 'avzpur'         # Advanced option: rsync flags
    shell: 'ssh'
    # or
    #shell: '/usr/bin/sshpass -p password ssh -o StrictHostKeyChecking=no -l username'
trigger:                    # Triggers fire after uploading file successfully
                            # which STARTS with following patterns

    "*": "uptime"            # Wildcard trigger for any file uploaded

    "resources/scripts/coffee": [   # Any file under %PROJECT_ROOT%/resources/scripts/coffee \
                                    # being uploaded will fire this trigger

        "echo Compile coffeescript to js ..."
        "coffee -b --output js/ --compile coffee/"
        "ls public/js/|xargs -I@ echo \\t@"       # You can also pipe commands but don't \
                                                  # forget to escape special characters
    ]
    "resources/scripts/sass": [
        "echo Compile sass to css ..."
        "sass --update resources/scripts/sass:public/css"
    ]
```

### Introduction to Trigger ###
#### Config ####
```
trigger:
    "*": [
        "echo \'<span class=warning>Every time you\\'ll see me</span>\'"
    ]
    "coffee": [
        "echo Compile coffeescript to js ..."
        "mkdir -p js"
        "coffee -b --output js/ --compile coffee/"
        "ls js/|xargs -I@ echo \\t@"
    ]
    "sass/style.sass": [
        "echo Compile sass to css ..."
        "mkdir -p css"
        "sass --update sass:css"
    ]
```
#### Result ####
![trigger](https://cloud.githubusercontent.com/assets/586262/14584004/a2cf2872-0466-11e6-9908-5f035a8b4e46.gif)

### Keybindings ###
* `ctrl`+`alt`+`l` (Windows/Linux) `cmd`+`alt`+`l` (Mac) Toggle log window

### Known Problems ###
* You have to `Sync Local -> Remote` manually after renaming and deleting files.

### Roadmap ###
* Listen to events
  * Create folders
  * Rename files/folders
  * What about deleting?
