# my-pure-data-source-studies

This is some notes i wrote while studying PD's source. Right know **it is NOT intended to be something read by others** but in the future i have plans to turn it on a development guide and organize it so everyone can contribute with PD's source code knowledge.

Let me leave this here, temoprarily: [Github Markdown Same Page Link](https://stackoverflow.com/questions/27981247/github-markdown-same-page-link)

> **How to use it?**
>
> Click what you're looking for in the index to go to it's respective file. To go back, click the header and it should bring you to this page, to it's position on the index


# Index

- <a id="index-api">[The API](https://github.com/HenriAugusto/my-pure-data-source-studies/blob/master/API.md#the-api)</a>
   - [m_pd.h](https://github.com/HenriAugusto/my-pure-data-source-studies/blob/master/API.md#m_pdh)
   - [m_imp.h](https://github.com/HenriAugusto/my-pure-data-source-studies/blob/master/API.md#m_imph)
   - [m_pd.h vs m_imp.h](https://github.com/HenriAugusto/my-pure-data-source-studies/blob/master/API.md#m_pdh-vs-m_imph)
- <a id="index-classes-and-objects">[Classes and Objects](#classes-and-objects)</a>
- <a id="index-glist">[t_glist](https://github.com/HenriAugusto/my-pure-data-source-studies/blob/master/t_glist.md#glist)</a>
   - <a id="index-t_editor">[t_editor](#t_editor)</a>
- <a id="index-gui">[GUI](#gui)</a>


## [GUI](#index-gui)

PD has two separate parts for its gui. The C part and the TCL part. They communicate bla bla bla (finish this)

* **g_editor.c**: Code for dealing with the editor (code responsible for connecting objects and etc)
   * the function **canvas_key()** is responsible for receiving the keyboard and mouse input from the user
* **g_text.c**: Code for dealing wit the patchable objects!
   * the function **glist_drawiofor()** is the function responsible for telling the TCL side what to draw!
   
See the **proc ::pd_bindings::sendkey** inside `pd_bindings.tcl` file and investigate how we can send the "alt" key state to canvas_key!

### Inlets and Outlets positioning

The mathematical formula for positioning inlets and outlest is located in the method

**glist_drawiofor** in the file `g_text.c`.

I still didn't figure out how PD knows if the mouse is inside an inlet/outlet.

anyway i should check tryconnect inside g_editor
