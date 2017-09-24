
# This actualy works with the latest Sierra (macOS 10.12.6)

On OS X systems where the optical drive has been removed to make room for a solid state drive (SSD) forces the user to access the optical drive via a USB/Thunderbolt enclosure. While this works as intended, the native OS X DVDPlayer will *NOT* allow the playing of DVD media (movies) in the out of the box configuration.

If you have a Macintosh without DVD Superdrive you can run into the same situation even you didn't remove any component from your Mac.

Many solutions are available on the Internet sites but since Apple introduced the rootless mode (or different name SIP - System Integrity Protection) on many of the system relevant files and folders, those solutions are not workable solutions anymore.

This document provides the directions to switch the DVDPlayer app to restrict playing from INTERNAL optical drives to EXTERNAL ones. For use cases where the optical drives being actively used are both internal and external, this procedure will really not help. In that case a different application would be a better choice. The VLC app seems to handle these cases quite well.

If you want to use Apple's DVD Player [link to download](https://github.com/fehervaria/DVDDriveSwitcher_macOS/blob/master/DVDPlayerApp.zip)  or any third party application doesn't work give a try to my researches.

After studiing the main idea (modify the DVDPlayback framework), the DVD Drive Switcher and the macOS system structure I find the following way workable. Please read it fully and then do it if you agree with the risks.

My idea is the following:

1. Turn off the System Integrity Protection as short time as possibly
2. Copy the DVDPlayback framework out from the /System/Library path 
3. Create an alias (symlink) to the DVDPlayback framework in its original place
4. Use a modified version of the DVD Drive Swither to exchange internal/external usage of the DVD drive.

RISK: When you disable the System Integrity Protection (csrutil disable / csrutil enable) your system is unprotected from possible system level changes. PLEASE follow this instruction if you agree with this risk. At the and of the changes, please reenable the System Integrity Protection with the csrutil enable command.

I will give Terminal instructions and Finder actions too, use it for your own comfort.

So, lets do it:

0. Download "DVD Drive Switcher" app script from here: http://www.fieldsnet.com/a-valid-dvd-drive-could-not-be-found/

   The link is at the bottom of the text, update from May 20, 2013. 
   or direct link: http://www.fieldsnet.com/wp-content/uploads/2012/05/DVDDriveSwitcher.zip


1. Create a Frameworks folder in your home's Library folder

   * In the Finder, click the Go menu in the menu bar, press and hold the Option key, then choose Library. In the window opened, create a new folder "Frameworks"

   * In the Terminal use this command: 

     ```
     mkdir ~/Library/Frameworks
     ```

2. Copy the original DVDPlayback.framework from your /System/Library/Frameworks folder into your, just created, Frameworks folder

   * In the Finder, click the Go menu, choose Go to Folder (or press Shift + Command + G) and in the poped up window type: ~/Library/Frameworks. Open another Finder window and navigate to your system hard drive's /System/Library/Frameworks folder. Locate the DVDPlayback.framework and drag it to the other Finder window - into your Library/Frameworks folder.

   * In the terminal

     ```
     cp -R /System/Library/Frameworks/DVDPlayback.framework ~/Library/Frameworks/DVDPlayback.framework
     ```

3. Reboot your computer to Recovery mode. Reboot and press and hold Command+R

4. Open the Terminal from the from the Utilities menu and disable the System Integrity Protection

   ```
   csrutil disable
   ```

5. Reboot your computer to your user.

6. Move the original DVDPlayback.framework to your desktop. (This is the backup from the original, don't delet it)

   * In the Finder navigate to the /System/Library/Frameworks (press Shift + Command + G and type the path and press Enter). Locate the DVDPlayback.framework and drag&drop to your desktop.

   * in the Terminal:

     ```
     sudo mv /System/Library/Frameworks/DVDPlayback.framework ~/Library/Frameworks/
     ```

7. Create and alias (symlink) to the copy of DVDPlayback framework in your Library/Frameworks:

   * In the Finder navigate to your Library/Framework folder: press Shift + Command + G and type ~/Library/Frameworks. Then press and hold down the Option and Command and drag the DVDPlayback.framework to create the alias side by it in your Library/Frameworks folder. Drag&Drop the created alias into the /System/Library/Frameworks folder.

   * In the Terminal:

     ```
     ln -f -s ~/Library/Frameworks/DVDPlayback.framework /System/Library/Frameworks/DVDPlayback.framework
     ```

8. Reboot your computer to Recovery mode. Reboot and press and hold Command+R

9. Open the Terminal from the from the Utilities menu and disable the System Integrity Protection

   ```
   csrutil enable
   ```

10. Reboot your computer to your user.

11. Open the Script Editor application from your Utilities folder (inside the Applications folder)

12. Modify the first line of the script - but don't change anything else:

    ```
    property DVDPlayer : "~/Library/Frameworks/DVDPlayback.framework/Versions/A/DVDPlayback"
    ```
    The complete code of the modified script should look like this:

    ```
    property DVDPlayer : "~/Library/Frameworks/DVDPlayback.framework/Versions/A/DVDPlayback"

    set decision to display dialog ("Which DVD drive do you want to use?") buttons {"Internal", "External", "Cancel"} default button 2 with icon note
    if button returned of decision is "Internal" then
        do shell script "perl -pi -e \"s|\\x45\\x78\\x74\\x65\\x72\\x6e\\x61\\x6c|\\x49\\x6e\\x74\\x65\\x72\\x6e\\x61\\x6c|g\" " & (quoted form of DVDPlayer) with administrator privileges
    else if button returned of decision is "External" then
        do shell script "perl -pi -e \"s|\\x49\\x6e\\x74\\x65\\x72\\x6e\\x61\\x6c|\\x45\\x78\\x74\\x65\\x72\\x6e\\x61\\x6c|g\" " & (quoted form of DVDPlayer) with administrator privileges
    end if
    ```

13. Save your modified script to where do you need or [download it](https://github.com/fehervaria/DVDDriveSwitcher_macOS/blob/master/DVDDriveSwitcher.zip).

14. DONE. Start the script with double click and select the External if you have an USB DVD drive connected.

15. Try your Apple DVD Player application. Should work now.

16. If everything works, then archive the original DVDPlayback.framework stored on your Desktop. (In case you need, put it back to the original place)



Sources:

http://www.fieldsnet.com/a-valid-dvd-drive-could-not-be-found/

https://blog.philippklaus.de/2013/02/be-able-to-watch-dvds-with-an-external-dvd-drive-on-mac-os-x

https://discussions.apple.com/thread/7110515?start=0&tstart=0

https://www.cnet.com/news/addressing-dvd-player-error-70012-when-using-external-drives-in-os-x/
