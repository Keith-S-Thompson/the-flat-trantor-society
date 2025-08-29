<!-- Title: Where should the control key be? -->
<!-- URL:   http://the-flat-trantor-society.blogspot.com/2013/12/where-should-control-key-be.html -->

Almost all modern computer keyboards place the Caps Lock key
immediately to the left of **A**, with the Shift key below it (next
to **Z**) and the Control key below that, in the lower left corner.

It wasn't always this way.

For example, many of Sun's keyboards ([images
here](http://xahlee.info/kbd/sun_microsystems_keyboard.html)) put the
Control key immediately to the left of **A**, and the Caps Lock key
in the lower left corner.

If you happen to like the "modern" layout, that's great; I'm not going
to try to change your mind, and you can feel free to stop reading now.

But personally, I find it *much* easier to type when the Control
key is immediately to the left of the **A** key, and the Caps Lock
(which I hardly ever use) is either safely out of easy reach or
disabled altogether.  I use control sequences extensively. I'm a
heavy user of vim, I occasionally use Emacs, and I use Emacs-style
key bindings in the bash shell.  Reaching my left pinky finger down
below the shift key every few seconds is quite awkward, but if the
control key is on the home row I don't even have to think about it.
Yes, I've tried using keyboards with Control below Shift; no, I've
never been able to get used to it.

Fortunately, there are ways to remap your keyboard in software so that
the key labeled "Caps Lock" acts as a Control key.  Unfortunately,
those ways vary considerably from one operating system to another.

<!-- more -->

- **Microsoft Windows 7 through 10**:

  Microsoft Windows does let you do some limited keyboard remapping
  through the Control Panel (in Windows 7 at least, it's under "Region
  and Language", not under "Keyboard") -- but for some unfathomable
  reason there's no option to remap the Caps Lock and Control keys.

  You can swap the Control and Caps Lock keys, or make
  Caps Lock an additional Control key, by modifying the
  system registry.  I provide instructions for doing so
  [here](https://github.com/Keith-S-Thompson/no-caps-lock).
  Unfortunately, this is a system-wide setting; it doesn't let you
  change the layout for an individual user.  I advise *not* applying
  this registry patch to a shared Windows system unless you're sure that
  all users of the system are ok with a "non-standard" keyboard layout.

  The Windows PowerToys Keyboard Manager is another option.  See below.

- **Microsoft Windows 11**:

  Windows 11 does not have the same registry key mentioned above for
  Windows 10 and lower.

  I have a laptop that originally had Windows 10 installed, and I had
  applied the above registry hack.  When I later upgraded the system to
  Windows 11, it continued to work, but I don't know how.  When I got
  a Windows 11 laptop for work, I was unable to apply the same hack.

  A solution that *mostly* works on Windows 11 (and probably on
  Windows 10 as well) is to use the Windows PowerToys Keyboard Manager.
  Its "Remap Keys" feature is fairly straightfoward, and can be used to
  swap the Control and Caps Lock keys or to make both act like Control.

  I've found that this mapping, unlike the registry hack, does not
  apply in all contexts.  In particular, it's not active on the
  login screen.

  There's also a possible issue where caps lock can still be engaged:  
  [Remapping caps lock with Keyboard Manager occasionally
  still enables caps lock when pressing the remapped
  key. #17982](https://github.com/microsoft/PowerToys/issues/17982)

  Due to these issues, I suggest mapping *some* key to Caps Lock, so
  it can be turned off when needed.  I've used the "Remap a shortcut"
  feature (distinct from "Remap a key") to map Alt-. to Caps Lock.

- **Linux** (or GNU/Linux if you prefer):

  Fortunately, Linux-based systems generally *do* let you modify
  keyboard layouts on a per-user basis.  The specific method can vary
  depending on which distribution and desktop environment you use.
  One of the following methods is likely to work.

  See also [this question](http://unix.stackexchange.com/questions/114022/map-caps-lock-to-control-in-linux-mint)
  and [this answer](http://unix.stackexchange.com/questions/114022/map-caps-lock-to-control-in-linux-mint/114023#114023)
  on [unix.stackexchange.com](http://unix.stackexchange.com).

  **UNIX-like command-line solutions:**

  Either of the following commands should work to map Caps Lock to
  Control (making both keys act like a Control key) for the duration
  of the current X session:

    xmodmap -e 'clear Lock' \
            -e 'keycode 0x42 = Control_L' \
            -e 'add Control = Control_L'

  or:

    setxkbmap -option ctrl:nocaps

  I think the `setxkbmap` command is newer; you might have to resort to
  `xmodmap` for some older systems.

  I think that

    setxkbmap -option ctrl:swapcaps

  will swap the Control and Caps Lock keys, but I haven't tried it..

  Both of these have the drawback that the behavior will revert to
  the default when the current X session terminates (typically when
  you log out or reboot).  You can either re-execute the command on
  startup, or arrange for the system to do it for you.

  I find it more convenient, where possible, to do this through the
  desktop GUI, so the setting is persistent across reboots.

  **Debian 6, Gnome desktop**:
    - "System" > "Preferences" > "Keyboard"
    - Select the "Layouts" tab
    - Highlight the layout you use (mine is "USA")
    - Click the "Options" button
    - Under "Ctrl key position", select "Make CapsLock an additional
      Ctrl", or whichever option you prefer.

  **Linux Mint 14, Cinnamon desktop**:
    - From the "System Tools" menu, select "System Settings", then
      open "Keyboard Layout"
    - Select the "Layouts" tab
    - Click the "Options..."  button.
    - Open "Caps Lock key behavior" and select the
      option you prefer.  I use "Make Caps Lock an additional Control but
      keep the Caps_Lock keysym", which makes both Caps Lock and Control
      act as a Control key.

  **Linux Mint 15, Cinnamon destkop**:
    - From the "System Tools" menu, select "System Settings", then
      open "Regional Settings"
    - Select the "Layouts" tab
    - Click the "Options..."  button.
    - Open "Caps Lock key behavior" and select the
      option you prefer.  I use "Make Caps Lock an additional Control but
      keep the Caps_Lock keysym", which makes both Caps Lock and Control
      act as a Control key.

  **Linux Mint 16, KDE desktop**:
    - From the main menu, select "Applications", then "Settings", then "System Settings".
    - Under "Hardware", open "Input Devices"
    - Keyboard settings are shown by default; open the "Advanced" tab.
    - Click the "Control keyboard options" checkbox.
    - Open "Ctrl Key Position"
    - Enable and select "Caps lock as Ctrl" or "Swap Ctrl and Caps Lock"

  **Linux Mint 17, Xfce desktop**:

  Oddly, the Xfce settings GUI doesn't seem to have an option to change
  the behavior of the Caps Lock key.  See "**UNIX-like command-line
  solutions**" above.

  Modifying `/etc/default/keyboard` will affect all users on the system.

  **Ubuntu 19.04, Cinnamon desktop**:
    - From the main menu, select "System Settings"
    - Search for "keyboard"
    - Select the "Layouts" tab
    - Select "Options..."
    - Open "Ctrl position"
    - Enable and select "Caps lock as Ctrl" or "Swap Ctrl and Caps Lock"

  **Linux virtual console**:

  [This web page](http://www.emacswiki.org/emacs/MovingTheCtrlKey#toc7)
  discusses various ways to remap the control key in the Linux
  virtual console.  (This is the text-only console reachable by typing
  Ctrl-Alt-F1, Ctrl-Alt-F2, etc.).  The most straightforward method
  seems to be:
    - Add the line `XKBOPTIONS="ctrl:nocaps"` to `/etc/default/keyboard`
    - `$ sudo dpkg-reconfigure -phigh console-setup`

  Replace `nocaps` by `swapcaps` if you prefer to swap Control and
  Caps-Lock rather than making both keys act like Control keys.

  I've tried this on Debian 6 and Ubuntu 14.03.3 LTS, and it works after a reboot.

- **Mac OS X 10.5.8**:
    - System Preferences
    - Keyboard & Mouse
    - Keyboard tab > Modifier Keys ...
    - Change Caps Lock to act as Control
    - Optional: Change Control to act as Caps Lock

  This article: [http://jilles.me/tmux-for-developers/](http://jilles.me/tmux-for-developers/)
  discusses remapping the caps lock key on Mac OS X.
