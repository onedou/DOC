origin: https://www.macbed.com/if-crashes-when-opening/

If Crashes when opening
July 13, 2019

 

Apple removed TNT’s certificate, so the app will crash after July 12th. The current solution is to sign it yourself.

Run in Terminal

codesign --force --deep --sign - /Applications/name.app


Requisite: Xcode or the Apple Command Line Tools
To install, execute

xcode-select --install

in the terminal emulator of your choice, and the macOS GUI will give you the option to install Xcode (from the Mac App Store) or the CLTs. If you install Xcode, launch it at least once to complete the installation and agree to the license.


Alternatively, you can use CodeSigner to sign some apps.

Installation instructions:
downloaded CodeSigner, then mount the DMG volume
Copy CodeSigner.app from the mounted DMG volume into one of your applications paths; recommended: ~/Applications/Utilities/
If you are using macOS Finder or a similar application with Services support as your main file manager, double-click the CodeSigner workflow: a window titled Quick Action Installer will appear asking you if you want to install it; click Install. You can assign a keyboard shortcut to the Quick Action in System Preferences > Keyboard > Shortcuts > Services > Files and Folders > CodeSigner
If you also want the ability to manually run CodeSigner in a terminal emulator—example:
codesigner /Applications/Parallels\ Desktop.app
—copy the codesigner shell script into your $PATH, e.g. /usr/local/bin/
On Mojave please allow CodeSigner to control System Events; this is necessary for GUI prompts to work via AppleScript

 

What’s New
Version 0.9.3 beta 4:

CodeSigner will now grab an application's or file's icon for the notifications
Bug fix: account for terminal-notifier display error in case of filenames with leading double-quotes (thanks to roryokane)
If codesigner doesn't execute as a Platypus app, it will search for terminal-notifier only in the default macOS Applications paths, and in installation directories for Homebrew, MacPorts and Fink (thanks to roryokane)

Compatibility
OS X 10.8 or later, 64-bit processor


Screenshots

