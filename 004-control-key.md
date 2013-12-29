<!-- Title: Where should the control key be? -->
<!-- URL:   http://the-flat-trantor-society.blogspot.com/2013/12/where-should-control-key-be.html -->

Almost all modern computer keyboards place the Caps Lock key
immediately to the left of **A**, with the Shift key below it (next
to **Z**) and the Control key below that, in the lower left corner.

It wasn't always this way.

For example, many of Sun's keyboards ([images
here](http://xahlee.info/kbd/sun_microsystems_keyboard.html) put the
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

- Microsoft Windows:

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

- Linux:

  (or GNU/Linux if you prefer)

  Fortunately, Linux-based systems generally *do* let you modify keyboard
  layouts on a per-user basis.  The specific method can vary depending on
  which distribution and desktop environment you use.  I'm running Linux
  Mint 14 on my home system; other Linux-based systems should be similar.

  From the "System Tools" menu, select "System Settings", then
  open "Keyboard Layout".  Select the "Layouts" tab, then click the
  "Options..."  button.  Open "Caps Lock key behavior" and select the
  option you prefer.  I use "Make Caps Lock an additional Control but
  keep the Caps_Lock keysym", which makes both Caps Lock and Control
  act as a Control key.

- MacOS:

  I'll add instructions for MacOS later.
