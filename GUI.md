- <a id="index-gui">[GUI](#gui)</a>
   - <a id="index-c-side">[C side](#c-side)</a>
      - <a id="index-inlets-and-outlets-positioning">[inlets and outlets positioning](#inlets-and-outlets-positioning)</a>
   - <a id="index-tcl-side">[TCL side](#tcl-side)</a>
      - <a id="index-how-the-gui-knows-what-to-draw">[How the GUI knows what to draw](#how-the-gui-knows-what-to-draw)</a>
         - <a id="index-creating-objects">[Creating Objects](#creating-objects)</a>

Back to the [**main index**](https://github.com/HenriAugusto/my-pure-data-source-studies/blob/master/README.md#index-api)

# [GUI](#index-gui)

PD has two separate parts for its gui. The C part and the TCL part. They communicate bla bla bla (finish this)

* **g_editor.c**: Code for dealing with the editor (code responsible for connecting objects and etc)
   * the function **canvas_key()** is responsible for receiving the keyboard and mouse input from the user
* **g_text.c**: Code for dealing wit the patchable objects!
   * the function **glist_drawiofor()** is the function responsible for telling the TCL side what to draw!
   
See the **proc ::pd_bindings::sendkey** inside `pd_bindings.tcl` file and investigate how we can send the "alt" key state to canvas_key!

## [C side](#index-C-side)

### [Mouse Input: handling]()

[`canvas_doclick`](https://github.com/pure-data/pure-data/blob/7c27aa0ad505bb4802eee3fc40886836c814353f/src/g_editor.c#L2286)

### [Inlets and Outlets positioning](#index-inlets-and-outlets-positioning)

The mathematical formula for positioning inlets and outlest is located in the method

**glist_drawiofor** in the file `g_text.c`.

I still didn't figure out how PD knows if the mouse is inside an inlet/outlet.

anyway i should check tryconnect inside g_editor

## [TCL side](#index-tcl-side)

### [How the GUI knows what to draw](#index-how-the-gui-knows-what-to-draw)

The [C side](#c-side) of the GUI tells each canvas what to draw by creating (and modifying) widgets on it.

#### [Creating objects](#index-creating-objects)

when you're typing in an object there are actually two widgets in the canvas.

One is a text one and the other is a rectangle one. The rectangle one has exactly the same tag but with an _"R"_ in the end. You can see it by using PD with the **"-d 1"** command line flag and editing an object.

Each text input is controlled by an [`t_rtext`](https://github.com/HenriAugusto/my-pure-data-source-studies/blob/master/t_rtext.md#t_rtext) struct and it's respective methods.

```tcl
set rectangle "$::current_tag"
append rectangle "R"
    ::pdwindow::post "::current_tag = $::current_tag\n"  ;#just so you can see it
    ::pdwindow::post "rectangle = $rectangle\n"          ;#just so you can see it
$::current_canvas itemconfigure $rectangle -fill red
$::current_canvas itemconfigure $::current_tag -fill blue
```

::current_tag along with ::current_canvas
