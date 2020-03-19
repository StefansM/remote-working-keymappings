# Key mappings for remote working

This repository contains some keybindings that I find useful for remote
working. I'm experiencing a problem making multiple remote hops (Citrix
Workspaces + Windows RDC) where modifier keys are intermittently dropped
between over the RDC link.

A [fix] for the issue is to tell Windows to interpret key combinations like
Alt+Tab on the source machine, instead of translating them to the destination
machine. However, doing so leaves you unable to use those key combinations.
There are some [alternatives], but I don't want to relearn decades of muscle
memory and ruin every other Windows computer for myself.

# Solution

The horrible, disgusting solution that I have here is to remap keys on my
local PC before they get sent to the remote desktop, and then reinterpret
them on the final machine in the chain.

This repository contains [xkb] configuration to remap local keys.

## Mappings

* <kbd>Alt</kbd> + <kbd>Tab</kbd> → <kbd>Alt</kbd> + <kbd>PgUp</kbd>

  Default Windows window-switcher.

* <kbd>Alt</kbd> + <kbd>Shift</kbd> + <kbd>Tab</kbd> → <kbd>Alt</kbd> + <kbd>PgDown</kbd>

  Default Windows window-switcher (reverse). Note: this actually sends
  <kbd>Alt</kbd> + <kbd>PgDown</kbd> because I don't understand xkb, but it seems
  to work.

* <kbd>LWin</kbd> → <kbd>RCtl</kbd>

  I use the left windows key for a lot of shortcuts, but this gets eaten by the
  jump box instead of being passed to the last workstation in the chain. This
  mapping changes the left windows key to the right control key, which I never
  use.

  This can then be reinterpreted by the trivial AutoHotKey script `RCtrl::LWin`
  on the final host.

## Usage

Apply the map using `xkbcomp`. Assuming this repository is cloned to `~/.xkb`:

    xkbcomp -I$HOME/.xkb ~/.xkb/keymap/remote $DISPLAY

Switch back using the `default` keymap:

    xkbcomp -I$HOME/.xkb ~/.xkb/keymap/default $DISPLAY

If you don't use a British keyboard, you'll need to change the mapping files to
use your layout.

## Mapping with i3

I use the i3 window manager at home, and a lot of the shortcut keys I use clash
with Windows shortcut keys. To get around this, I have an i3 mode set up that
disables all i3 shortcut keys when entered. You can tie the `xkbcomp` command
to the same mapping:

In `~/.config/i3/config`:

```
# Mod4 (Windows for me) + Scroll lock to enter remote mode
set $mode_remote Remote mode (⊞+Scroll Lock to escape)
bindsym Mod4+Scroll_Lock exec xkbcomp -I/home/user/.xkb /home/user/.xkb/keymap/remote ; mode "$mode_remote"

# Disable all i3 keys in this mode
mode "$mode_remote" {
  bindsym Mod4+Scroll_Lock exec xkbcomp -I/home/user/.xkb /home/user/.xkb/keymap/default :0 ; mode "default"
}
```

Note that I had to expand environment variables and tilde paths myself.


[fix]: https://superuser.com/questions/219461/shift-and-control-keys-out-of-sync-with-normal-keys-over-rdp
[alternatives]: https://docs.microsoft.com/en-us/windows/win32/termserv/terminal-services-shortcut-keys
[xkb]: https://www.x.org/wiki/XKB/
