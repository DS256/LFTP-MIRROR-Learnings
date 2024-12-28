# LFTP-MIRROR-Learnings

[LFTP](https://lftp.yar.ru/) is a useful utilty for providing backup over FTP. I have my own cloud web site where I set up accounts for each source. Internet access is only available with FTP.

I was attracted to the MIRROR option of LFTP to keep my LINUX account in sync with the backup on my cloud web server.

However, I had problems with

* Detail level of the documentation
* Lack of a formal user forum supporting discussions

The result was that I had to trial and error a working solution.

Here is my working BASH script

```
USER="user"                   #Your username
PASS="password"                #Your password
HOST="cloudSite"          #Keep just the address
LCD="/home/paul"                #Your local directory
RCD="/paul"                    #FTP server directory
LOG="/home/paul/pi_lib_ehost/paul_to_ehost.log"
LFT_LOG="/home/paul/pi_lib_ehost/paul_to_ehost_lft.log"

# Use --dry-run to prevent execution
lftp -f "
open $HOST
user $USER $PASS
set net:timeout 3600
mirror  \
    --verbose  \
    --continue --reverse --delete --no-symlinks --log=$LOG \
    --exclude-glob=.*/ \
    --exclude=Pictures/ \
    --exclude=Downloads/  \
    --exclude=Dropbox/ \
    --exclude=Videos/  \
    --exclude=MAC/vKeep/  \
    --exclude=MAC/vKeep_src/dest/  \
    --exclude=MAC/vKeep_src/photo/  \
    --exclude=Pgms/vTemp/venv1/  \
 $LCD $RCD
bye " &> $LFT_LOG

```
My learnings:

* The top reference in the `--exclude` must be a subdirectory off of the local directory. A full path name is skipped but not reported as an error. For example `--exclude=Pictures/` references `/home/paul/Pictures/` but it will not work if I add the `/home/paul` since that is the LCD.
* I was not able to get a single directory MIRROR'd. Best I got was LFTP was confused on what it should be mirroring.
* From various postings, if you want a directory to excluded, you must end the reference with a '/'. e.g. `--exclude=MAC/vKeep_src/photo/`
* I do not know REGEX so trying to understand the correct format was challenging. I finally found an online REGEX site where you can put in a pattern and text for testing. That help.
* The only difference with the -glob specifier or not is the inclusion of a '*'.
* `--exclude-glob=.*/` is a pattern I found in a post. It exludes all directories starting with a period '.'.
* I was not able to get an `--include` to work inside a higher level `--exclude`. e.g. a child subdirectory inside a parent directory
* Using `--verbose` along with `$lft ... &> file` provides more useful information than the `--log` oprion IMHO.
* Note the BASH line continuation character `\` for the MIRROR options
* `dry-run` is your friend when creating your script especially finding files and directories you thought you excluded.

That's it for now. I'll update as I find out more
