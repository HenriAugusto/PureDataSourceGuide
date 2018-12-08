# Building Pure Data

## GNU autotools

### Adding files

You just need to modify the .am files in each folder. The other files are auto generated. 

#### Adding .tcl file

If you're adding a .tcl don't forget to add the package to [_pkgIndex.tcl_](https://github.com/pure-data/pure-data/blob/master/tcl/pkgIndex.tcl).

And also on [_pd-gui.tcl_](https://github.com/pure-data/pure-data/blob/master/tcl/pd-gui.tcl#L36), which will look somewhat like this

```TCL
# Pd's packages are stored in the same directory as the main script (pd-gui.tcl)
set auto_path [linsert $auto_path 0 [file dirname [info script]]]
package require pd_connect
package require pd_menus
package require pd_bindings
[...]```




Tcl is interpreted so you do not need to build everytime you make a change. Just restart pd. 
