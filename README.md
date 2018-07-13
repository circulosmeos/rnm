# file renamer script for Windows and linux

Rename files (and folders) using Perl regular expressions (regex),
in linux and Windows, recursively (if desired) and with UTF-8 support.

A list of changes to be made is presented previous to any action, 
so that you can check that everything will be correctly renamed.

All the renaming actions can be exported to a text file.

## Use:

	rnm [-dlnNryY] 's/PATTERN/SUBSTITUTION/' 'FILTER' [PATH]

where FILTER is one of: {`/FILTER_REGEX/` | `LIST_PATTERN`}
depending on if `-r` or nothing is used (so FILTER_REGEX is needed)
or `-l` is used (so LIST_PATTERN is needed).

If PATH is not indicated, `.` is used.

Please note that in linux simple quotation marks are preferred: ''
whilst in Windows double quotation marks are needed: ""

**-d**: treat directory names when parsing and renaming.
    If it is not indicated, directory names are ignored.
    Versions previous to 2018-07.13 treat always directories.

**-l**: Use `ls` command modifiers as FILTER parameter, instead of a regex.
    Limitations of -l:
* USE WITH CAUTION as this JUST TRIES TO PARSE LS OUTPUT.
  Preferred method should be `rnm` or `rnm -r` in general.
* -l is not compatible with `rnm -r`
* In Windows, there must be a cygwin `ls` in the PATH, and UTF-8 
is not supported.

**-n**: when asked for confirmation a "NO" is automatically answered.
**-N**: as `-n`, but output is also written to a log file in current path.

**-r**: recursively filter all files and folders.
    If `-r` is not used, only files under current path are processed.

**-y**: when asked for confirmation a "YES" is automatically answered.
**-Y**: as `-y`, but output is also written to a log file in current path.

The command shows a list of changes to be made, 
which must be confirmed with 'y' (yes).
If capital 'Y' is entered, the results are written to a log file.

Parameters can also be indicated using stdin.
In this case PATH is compulsory.
The stdin input for parameters is needed in Windows in order to
use UTF-8 characters on regex, FILTER and/or PATH. Note that a cmd
console with UTF-8 support must be used. `CMD [/U]` isn't appropriate
in general, even with UTF-8 fonts. In Windows, if stdin is used,
`-[yY]` would be needed because input cannot be read from the keyboard.

## Example of output

As you can see, the command shows a list of actions to execute, and requires previous confirmation:

	C:\temp> rnm "$c++; s/^/$c./" "/txt$/"

	Using:
	        path:                   .
	        search pattern:         /txt$/
	        substitution regex:     $c++; s/^/$c./
	        recursive:              no
	        treat directories:      no

	.\Sorcières.txt         ->      .\0001.Sorcières.txt
	.\Люди Инвалиды.txt     ->      .\0002.Люди Инвалиды.txt
	.\夢の足音が聞こえる.txt    ->      .\0003.夢の足音が聞こえる.txt
	.\오렌지캬라멜.txt       ->      .\0004.오렌지캬라멜.txt

	--- (files =~ search pattern: 4)
	--- files to rename (listed): 4
	Do you want to rename this file list? (y/ Y(=>log)/ *):

## Examples of use:

	$ rnm 's/(\d{2}\.jpg)/\1/' '/^b.*\d{2}\.jpg$/' temp
This renames all jpg images in "temp" subdirectory which
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
starting with '0000'.

	$ rnm -r '$c=strftime "%Y%m%d", localtime(); s/(.+)/${c}.$1/' '/./'
This renames recursively all files and folders in the current directory,
prepending the current date to every name.

	$ rnm '$c=strftime "%Y%m%d", localtime(stat($f)->mtime); s/(.+)/$c.$1/' '/\.log$/'
This renames all files with ".log" extension in the current path,
prepending their file modification date to every name.

	$ rnm -l '$c=stat($f)->mtime; s/^/$c./' '*.log'
This renames all filenames with extension ".log" in the current 
directory, prepending to each one its modification unix timestamp. 

## Windows installation and notes
In order to use the script in Windows you need:

* a Perl installation (for example [ActivePerl](https://www.activestate.com/activeperl) or [Strawberry Perl](http://strawberryperl.com/)). The script has been tested with ActivePerl.
* copy the `rnm` script and `rnm.bat` to a folder in your execution PATH

The `rmm.bat` batch script is provided as an example: it supposes that `perl.exe` is in the PATH, and `rnm` must be preceded by its complete path.

In order to use `rnm -l` you need a [Cygwin](https://www.cygwin.com/) installation in your PATH, which provides the `ls` command.

Do not use `rnm -l` in Windows if you are planning to manage file names with UTF-8 characters.

If your are planning to manage file names with UTF-8 chars (mostly outside Latin-1), a console with proprer UTF-8 I/O must be used. Windows' `CMD.EXE` isn't 100% adequate, even after installing UTF-8 fonts (See [serganov fonts](https://math.berkeley.edu/~serganov/ilyaz.org/software/fonts/) and [metacpan UI-KeyboardLayout](https://metacpan.org/pod/distribution/UI-KeyboardLayout/lib/UI/KeyboardLayout.pm#The-console-font-configuration)), because some visual artifacts remain. Instead, [ConEmu]( https://conemu.github.io/) is suitable.

**Please, note that UTF-8 use in regex and code in Windows isn't yet perfect** (this warning doesn't refer to UTF-8 file names, which are correctly renamed):

For example, Perl code prepended to the regex parameter can fail if Windows file names have UTF-8 characters. For example with (note that simple and double quotations are changed from the above linux example):
	
	rnm "$c=strftime '%Y%m%d', localtime(stat($f)->mtime); s/(.+)/$c.$1/" "/./"

Nonetheless, if a pure regex without UTF-8 chars is used, all should run ok.

Also note that in order to use most UTF-8 chars directly in the regex or filter parameters, the stdin with a `|` must be used.
Note also that in Windows the use of stdin for input parameters to rnm command requires the use of `-[yYnN]` modifiers, because unfortunately input cannot be redirected to the keyboard.
For example, in Windows to substitute "の" japanese hiragana char with "の - " in all files and directories under 'temp2\\':
(Note though that -[yY] is needed in order to actually take actions):

	C:\temp> echo  -dr  "s/(の)/\1 - /"  "/./"  "temp2" |  rnm

## author
Written by [circulosmeos](mailto:loopidle@gmail.com)

## version
2018-07.13

## license
[GPL v3](https://www.gnu.org/licenses/gpl-3.0.en.html)

