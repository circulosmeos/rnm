# file renamer script for Windows and linux

Rename files (and folders) using Perl regular expressions (regex),
in linux and Windows, recursively (if desired) and with UTF-8 support.

A list of changes to be made is presented previous to any action, 
so that you can check that everything will be correctly renamed.

All the renaming actions can be exported to a text file.

## Use:

	rnm [-lr] 's/PATTERN/SUBSTITUTION/' {'/FILTER_REGEX/' | 'LIST_PATTERN'}

Please note that in linux simple quotation marks are preferred: ' ' 
whilst in Windows double quotation marks are needed: " "

**-r**: recursively filter all files and folders under current path.
    If "-r" is not used, only files under current path are processed.

**-l**: Use `ls` command modifiers as last parameter, instead of a regex.
Limitations of **-l**:

* -l is not compatible with `rnm -r`
* In Windows, there must be a cygwin `ls` in the PATH, and UTF-8 
    is not supported.

The command shows a list of changes to be made, 
which must be confirmed with 'y' (yes).
If capital 'Y' is entered, the results are written to a log file.

## Examples:

	$ rnm 's/(\d{2}\.jpg)/\1/' '/^b.*\d{2}\.jpg$/'
This renames all jpg images in the current directory which
name starts with letter 'b' and end with two numbers, for
just the two numbers and the extension.

There are some variables which can be used
in the `SUBSTITUTION_PATTERN`:

* `$c='0000'`, this variable is the only one available for writing in the regex
* `$counter` number of files renamed so far
* `$_` filename
* `$d` directory of the file (available only if using `-r`, or `-l` with `-R` or `*`)
* `$f` complete filename path

For example:

	$ rnm 's/^/$counter./' '/./'
This renames all files in current directory,
prepending a consecutive number starting with '0000'.

	$ rnm 's/^/($counter+1)."."/e' '/./'
This renames all files in current directory,
prepending a consecutive number starting with '1'.

	$ rnm -r '$c++; s/^/$c./' '/./'
This renames recursively all files and folders in the current directory,
prepending a consecutive number starting with '0001'.

	$ rnm -l 's/^/$counter./' '-rt *.jpg'
This renames all files with extension ".jpg" in current directory,
ordered by modification time, prepending a consecutive number 
starting with '0001'.

	$ rnm -r '$c=strftime "%Y%m%d", localtime(); s/(.+)/${c}.$1/' '/./'
This renames recursively all files and folders in the current path,
prepending the current date to every name.

	$ rnm '$c=strftime "%Y%m%d", localtime(stat($f)->mtime); s/(.+)/$c.$1/' '/\.log$/'
This renames all files with ".log" extension in the current path,
prepending their file modification date to every name.

	$ rnm -l '$c=stat($f)->mtime; s/^/$c./' '*.log'
This renames all filenames with extension ".log" in the current 
directory, prepending to each one its modification unix timestamp. 

## Windows installation
In order to use the script in Windows you need:

* a Perl installation (for example [ActivePerl](https://www.activestate.com/activeperl) or [Strawberry Perl](http://strawberryperl.com/)). The script has been tested with ActivePerl.
* copy the `rnm` script and `rnm.bat` to a folder in your execution PATH

The `rmm.bat` batch script is provided as an example: it supposes that both `perl.exe` and `rnm` are in the PATH.

In order to use `rnm -l` you need a [Cygwin](https://www.cygwin.com/) installation in your PATH, which provides the `ls` command.

Do not use `rnm -l` in Windows if you are planning to manage file names with UTF-8 characters.

## author
Written by [circulosmeos](mailto:loopidle@gmail.com)

## version
2018-06

## license
[GPL v3](https://www.gnu.org/licenses/gpl-3.0.en.html)

