# Compiling and Debugging on Windows

## Compiling

- See instructions in **INSTALL.txt** in the pd dir (and also **msw/README.txt** if you want to make the app in a different dir)
- After installed MinGW be sure to execute `mingw32.exe` instead of `msys2.exe`
- run `cd "path to your pd folder"` before typing commands (you can use shift+insert to paste)
- You only need to run make app (or msw-app.sh) once. Then you just change the core, run "make" and copy the `your_pd_dir/bin/pd.dll` into `your_pd_dir/pd-0.50.2/bin/`
   - Note that the .dll you are compiling should be significantly bigger than the one in the compiled folder (because it has all the debugging symbols and information)

- If you're using [git](https://git-scm.com/) all those things in your "pd-name_of_your_compile_folder" will appear as modifications in your working directory. Open the (hidden) `.git` folder and go open the ´info/exlude´ in your text editor and add `pd-*/` to it and save.


## Debugging

## gdb and gdbgui

### gdb

If you've followed the `INSTALL.txt` suggestion and is using MinGW then you already have [gdb](https://www.gnu.org/software/gdb/).

Keep in hand [this guide](https://condor.depaul.edu/glancast/373class/docs/gdb.html) (its more concise but misses `backtrack` / `bt`) or [this one](http://www.yolinux.com/TUTORIALS/GDB-Commands.html) which is bigger and more complete

- open the MingW2
- `cd "path_to_pd/bin"`
- `gdb pd`
   - (or `gdb --gdb_args "pd <pd_args>"` if you need to pass arguments to gdb or pd)
- `symbol-file D:/pd-debug/bin/pd.dll`
- `target exec D:/pd-debug/bin/pd.exe`
- optionally you can start with a breakpoint
   - `break canvas_key` (Or other function. You can also specify specific lines)
   - use `step`, `next`, `skip`, `continue`, etc
- run
- if the code segfaults: `backtrace` ( or `bt` ), `x`, etc

You will want to use paths without spaces on them.

### gdbgui

Open gdbgui and type the following commands.

- `symbol-file D:/pd-debug/bin/pd.dll`
- `target exec D:/pd-debug/bin/pd.exe`
- optionally you can start with a breakpoint
   - `break canvas_key` (Or other function. You can also specify specific lines)
   - use `step`, `next`, `skip`, `continue`, etc
- run
- if the code segfaults: `backtrace` ( or `bt` ), `x`, etc

Make sure 

- the paths doesn't contain spaces
- the paths are not inside ""
- the paths uses forward slashes (`/ ) instead of backward slashes (`\`)

### how to fix console behavior

Ok, you're in GDB but you notice some strang~er~ things happening. 

- You press the the up arrow keys to get the last command on your history and what happens? The cursor starts freaking out and moving up.
- You noticed you've made a typo and press the left keys to fix it and the cursor seems to move correctly but when you type some other freakishly behavior is observer.
- Your terminal is all B&W like a 20's picture.

Solution: use `winpty` (see [this issue](https://github.com/msys2/MINGW-packages/issues/5353))

In Mingw32 install `winpty` and them wrap your `gdb` calls with it!

```
pacman -S winpty
winpty gdb
```

Now the console works as expected and the output is colored.

### tips

- If you've forgot `symboml-file` and `target exec` you can just
   - `gdb pd`
   - `run`
   - close pd
   - symbols are loaded, you can set break points
- you can print variables!
   - let's say you're inside `iemgui_label`.
   - you can type print s->s_name to see what is the string in the `t_symbol`
- for printing a function or line in a file use `list <file>:<func/line>`
- use <kbd>tab</kbd> once or twice for autocompletion (of `gdb` commands and even of variable names!)
   
### Scripting

[reference](https://sourceware.org/gdb/current/onlinedocs/gdb/Command-Files.html#Command-Files)

You can automate some tedious tasks! If you're working on a specific function and want to always break at that function and set the display of the same variables, for example, it can get pretty tedious and repetitive running all the commands at the same time.

<details>
  <summary>Here's an example scripting file</summary>

```   
# http://sourceware.org/gdb/wiki/FAQ: to disable the
# "---Type <return> to continue, or q <return> to quit---"
# in batch mode:

symbol-file pd.dll
target exec pd.exe

# set a breakpoint at iemgui_label.
# it uses l (list) to show us more context of the function
# and sets display of some variables
b iemgui_label
commands 1
	l
	display s->s_name
	display iemgui->x_lab->s_name
	display iemgui->x_lab_unexpanded->s_name
end

# sets another breakpoint to show the backtrace and
# also sets the display of some variables
b iemgui_properties
commands 2
	bt
	display (*srl[0])->s_name
	display (*srl[1])->s_name
	display (*srl[2])->s_name
end

#to automatically start pd
run
```
</details>

Comments starts with `#`.

Put this code in the same folder as `pd.exe` with a _gdb_ extension and run

`gdb --command=myscript.dgb`

A good idea is to name the file the same as the branch you're testing.

This is a simple example just to illustrate command files. A "real" one might be more complicated. In the example the display command will be executed multiple times thus the variables will be displayed multiple times. (You can quit and run gdb again to fix that in case you don't want to put conditions in your code)
