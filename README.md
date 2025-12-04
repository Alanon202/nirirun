```nirirun``` is a small bash script to make manipulating windows with keybinds a bit easier on Niri. Similar to [nirius](https://git.sr.ht/~tsdh/nirius), only written in simple bash and not running as a daemon.

```nirirun``` relies on the premise that the same keybinds you use to launch programs can also be used to subsequently manipulate the state of those programs, and thus make keyboard-driven workflow adoption easier.

```nirirun``` can:

1. Focus on a window regardless of workspace
2. Move a window from any workspace to the current workspace and focus it
3. Cycle through different windows of the same program regardless of workspace
4. Focus on a window bound to the same workspace
5. Cycle through different windows of the same program bound to the same workspace

All by using the same keybind you used to launch the specific app.

# Usage

Place ```nirirun``` somewhere in your PATH to be able to reference it directly in Niri’s config, like in the examples. Otherwise, you will have to specify the location (e.g. ~/.config/scripts/nirirun).

By default ```nirirun``` will simply focus on the window in the workspace it is located in, or, if no app of that id is running, it will launch the program respecting any defined window rules (like launching in a particular workspace, or as floating, etc.):

```
binds {
    Ctlr+Alt+Delete { spawn "nirirun" "brave"; }
}
```

If passed the ```--move``` or ```-m``` parameter, it will move a program that is already running to the currently active workspace:

```
binds {
    Ctrl+Alt+Delete { spawn "nirirun" "-m" "xfce4-taskmanager"; }
}
```

This script requires app id’s to distinguish programs. In most cases the id is the same as the binary. Sometimes, however, a program will have an id that’s different. To get around this, we pass ```--class <app_id>``` or ```-c <app_id>``` and manually provide the class the program advertises:

```
binds {
    Mod+Z { spawn "nirirun" "-c" "io.gitlab.news_flash.NewsFlash" "newsflash"; }
}
```

You can use the title of an app instead of a class. Niri works better with id’s for window rule purposes, but matching by title is still useful for special windows with stable titles like Picture-in-Picture. Here ```nirirun``` will move a PiP window from the Zen Browser to the currently active workspace, if it exists:

```
binds {
    Mod+I { spawn "nirirun" "-n" "-m" "-t" "Picture-in-Picture" "zen-browser"; }
}
```

Note that this is a special carve-out for Picture-in-Picture, other functions like cycling through browser windows, if you use it, treat that PiP window just like any other browser window, and will cycle through it.

```nirirun``` focuses on the first window of a program it finds. If you have multiple windows of the same app open, you can focus on a window you didn’t want. That’s why you can also cycle between open windows of the same class. This is useful for example with multiple browser windows, as you can use the same shortcut to switch between multiple active windows. For this you use the ```--switch``` or ```-s``` parameter:

```
binds {
    Mod+Z { spawn "nirirun" "-s" "brave"; }
}
```

Note that you can combine the above with the ```--move``` parameter. In that case pressing the same key would first move the windows one by one to the active workspace, and begin cycling through them once they are all in the same workspace. This, however won’t solve the issue of not being able to fully control which of the opened windows you’re moving first.

You can also suppress the ability of ```nirirun``` to launch programs using ```-n``` or ```--no-launch```. This is especially useful if you want to define separate keybindings for launching and switching/moving functions. In this example, the first shortcut can launch an unlimited number of terminals, while the second shortcut will cycle through every one:

```
binds {
    Mod+T { spawn "foot"; }
    Shift+Mod+T { spawn "zsh" "-c" "nirirun -n -s -- foot"; }
}
```

Combining this with the ```--move``` parameter will give you the option of sequentially moving the terminals in the order of opening (I think). I’m not sure how useful this is in practice, but it is available.

```nirirun``` can also leverage the capabilities of modern terminals like ```foot``` or ```kitty``` (or presumably any other terminal that can set ```--app-id``` or ```--class```) and read that value directly, to make running and manipulating terminal programs easier. In this example ```nirirun``` infers the ranger app id from foot without an explicit ```-c``` parameter:

```
binds {
    Mod+R { spawn "zsh" "-c" "nirirun -m foot --title 'ranger' --app-id 'ranger' -e ranger"; }
}
```

Another option to use alongside ```--switch``` is ```--workspace``` or ```-w```. This will only cycle through windows of the respective app in the current workspace. If no other instance of the app exists in the current workspace, it'll also spawn a new one.

You can also leverage app-id capabilities to create window rules based on them.
