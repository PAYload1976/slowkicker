# slowkicker
glFTPd Slowkicker. Original code by bioboy. Small changes and this README by i137.


### Installation:

1. In *Makefile*:
   - Change GLFTPD_PATH if your glftpd root path is not /glftpd.

2. In *slowkicker.cpp*:
   - Change LOG_FILE if your glftpd logs dir is not /glftpd/ftp-data/logs/.
   - It makes sense to keep the LOCK_FILE in the tmp dir, so don't touch that.
   - Change IPC_KEY if you're not using glftpd's default key (0x0000DEAD).
   - Change DIRECTORIES[] to match your requirements. You may have to experiment with the values to find a good "fit".

3. `make` and `make install` to install Slowkicker into your glftpd bin dir.

4. If you don't already have a *tmp* dir in your glftpd dir, then `sudo mkdir -m777 /glftpd/tmp`.

5. `sudo touch /glftpd/ftp-data/logs/slowkicker.log` and `sudo chmod 666 /glftpd/ftp-data/logs/slowkicker.log` for good measure, I don't remember if it's created automatically.

6. In *ngBot.conf*:
   - Add the 3 msg types for the Slowkicker log events to the msgtypes(DEFAULT) variable:
     ```
     set msgtypes(DEFAULT) "... SLOW ZEROBYTE STALLED"
     ```
   - Add variables for the different announces, and if you want the announces redirected to a different channel:
     ```
     set variables(SLOW)       "%path %username %groupname %sourceip %speed"
     set variables(ZEROBYTE)   "%path %username %groupname %sourceip"
     set variables(STALLED)    "%path %username %groupname %sourceip"
     set redirect(SLOW)        "$mainchan"
     set redirect(ZEROBYTE)    "$mainchan"
     set redirect(STALLED)     "$mainchan"
     ```

7. In *yourthemefile.zst*:
   - Add the announce for the Slowkicker message types (the %sourceip and %path variables are removed here):
     ```
     announce.SLOW             = "[%b{SLOWKICK}] >> kicked %b{%username}/%groupname ~ %speed"
     announce.ZEROBYTE         = "[%b{SLOWKICK}] >> kicked %b{%username}/%groupname ~ 0-byte file"
     announce.STALLED          = "[%b{SLOWKICK}] >> kicked %b{%username}/%groupname ~ transfer stuck"
     ```

8. Rehash your Eggdrop bot, then go to your glftpd *bin* dir and `./slowkicker`.

9. If you want it to start automatically, add it as a cron job:
   `sudo crontab -e` and add:
   ```
   */30 * * * * /glftpd/bin/slowkicker >/dev/null 2>&1
   ```
   This job runs every 30 minutes, but if Slowkicker is already running, it won't start a new process.


### Support
Shouldn't need much support, but try #glhelp on EFNet, maybe someone's around that knows about this project.
