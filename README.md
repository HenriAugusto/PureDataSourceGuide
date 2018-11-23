# my-pure-data-source-studies
This is some notes i wrote while studying PD's source. Right know it's NOT intended to be something read by others but in the future i have plans to suggest a development guide

## Classes and Objects

PD is written in C so there is no classes like you would find in C++ or Java for example.

PD's graphical objects are named **t_gobj** (m_pd.h)??

Actually i'm not sure because of m_pd.c line 228

```C
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

```C
   /* t_object is synonym for t_text (LATER unify them) */

typedef struct _text t_object;

#define ob_outlet te_outlet
#define ob_inlet te_inlet
#define ob_binbuf te_binbuf
#define ob_pd te_g.g_pd
#define ob_g te_g
```
(note the defines that allows you to access the `_text` members with a new `ob_`prefix and new names)

and just before that

```C
typedef t_class *t_pd;      /* pure datum: nothing but a class pointer */

typedef struct _gobj        /* a graphical object */
{
    t_pd g_pd;              /* pure datum header (class) */
    struct _gobj *g_next;   /* next in list */
} t_gobj;
```

So inside **t_object** (*_text*) there is a **t_gobj**, which is a linked list. 

Still i can't figure what **t_pd** is.

## glist

/* this file defines the structure for "glists" and related structures and
functions.  "Glists" and "canvases" and "graphs" used to be different
structures until being unified in version 0.35.

/* the t_glist structure, which describes a list of elements that live on an area of a window

The struct is on line 154

[g_canvas.h](https://github.com/pure-data/pure-data/blob/master/src/g_canvas.h#L154) line 154

As it can be seen on [m_pd.h](https://github.com/pure-data/pure-data/blob/7c27aa0ad505bb4802eee3fc40886836c814353f/src/m_pd.h#L208):

- `t_glist` is a const #define to `struct _glist`
- `t_canvas` is also a const #define to `struct _glist`

### getting the selected objects

in the file `g_canvas.h` there is the

```C
typedef struct _selection
{
    t_gobj *sel_what;
    struct _selection *sel_next;
} t_selection;
```

which allows you to access the selection

```C
t_canvas *x;
t_selection *y;
for (y = x->gl_editor->e_selection; y; y = y->sel_next)
    {
        gobj_displace(y->sel_what, x, dx, dy);
    }
```

### object creation

objects/messages/floatatoms/comments are created in the functions (located in `g_text.c`):

* **canvas_obj:**
* **canvas_msg:**
* **canvas_atom:** what?
* **canvas_floatatom:**
* **canvas_symbolatom:**
* **canvas_bng:**

## GUI

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
