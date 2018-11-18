# my-pure-data-source-studies
This is some notes i wrote while studying PD's source. Right know it's NOT intended to be something read by others but in the future i have plans to suggest a development guide

## Classes and Objects

PD is written in C so there is no classes like you would find in C++ or Java for example.

PD's graphical objects are named **t_gobj** (m_pd.h)??

Actually i'm not sure because of m_pd.c line 228

```
typedef struct _text        /* patchable object - graphical, with text */
{
    t_gobj te_g;                /* header for graphical object */
    t_binbuf *te_binbuf;        /* holder for the text */
    t_outlet *te_outlet;        /* linked list of outlets */
    t_inlet *te_inlet;          /* linked list of inlets */
    short te_xpix;              /* x&y location (within the toplevel) */
    short te_ypix;
    short te_width;             /* requested width in chars, 0 if auto */
    unsigned int te_type:2;     /* from defs below */
} t_text;
```

and also m_pd.c line 249

```
   /* t_object is synonym for t_text (LATER unify them) */

typedef struct _text t_object;
```

So i'm not sure PD uses **t_gobj** or **t_object/_text**
## glist

/* this file defines the structure for "glists" and related structures and
functions.  "Glists" and "canvases" and "graphs" used to be different
structures until being unified in version 0.35.

/* the t_glist structure, which describes a list of elements that live on an area of a window

The struct is on line 154

(g_canvas.h)[https://github.com/pure-data/pure-data/blob/master/src/g_canvas.h] line 154

## GUI

PD has two separate parts for its gui. The C part and the TCL part. They communicate bla bla bla (finish this)

* **g_editor.c**: Code for dealing with the editor (code responsible for connecting objects and etc)
   * the function **canvas_key()** is responsible for receiving the keyboard and mouse input from the user
* **g_text.c**: Code for dealing wit the patchable objects!
   * the function **glist_drawiofor()** is the function responsible for telling the TCL side what to draw!
   
See the **proc ::pd_bindings::sendkey** inside `pd_bindings.tcl` file and investigate how we can send the "alt" key state to canvas_key!
