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

There's an internal variable `$c='0000'` available, which can be used
in the `SUBSTITUTION_PATTERN`.
For example:

	$ rnm -l '$c++; s/^/$c./' '-rt'
This renames all files (and folders) in current directory,
ordered by modification time, prepending a consecutive number 
starting with '0001'.

	$ rnm -l '$c=stat($_)->mtime; s/^/$c./' '*.log'
This renames all filenames with extension ".log" in the current 
directory, prepending to each one its modification unix timestamp. 

## Windows installation
In order to use the script in Windows you need:

* a Perl installation (for example [ActivePerl](https://www.activestate.com/activeperl) or [Strawberry Perl](http://strawberryperl.com/)). The script has been tested with ActivePerl.
* copy the `rnm` script and `rnm.bat` to a folder in your execution PATH

The `rmm.bat` batch script is provided as an example: you may need to add the path to your Perl executable if it is not in your PATH.

Do not use `rnm -l` in Windows if you are planning to manage file names with UTF-8 characters.

## author
Written by [circulosmeos](mailto:loopidle@gmail.com)

## version
2018-06

## license
[GPL v3](https://www.gnu.org/licenses/gpl-3.0.en.html)

