# my-pure-data-source-studies

This is some notes i wrote while studying PD's source. Right know **it is NOT intended to be something read by others** but in the future i have plans to turn it on a development guide and organize it so everyone can contribute with PD's source code knowledge.

## The API

The first thing to notice is that there aren't much header files (.h) . The ones in the `/src` directory are
g_all_guis.h, g_canvas.h, g_undo.h, m_imp.h, m_pd.h, s_audio_alsa.h, s_audio_paring.h, s_stuff.h, s_utf8.h, x_vexp.h.

The main ones are

- **m_pd.h**
- **m_imp.h**
- **g_canvas.h**

### m_pd.h

This file inclues the **public** API. That is, stuff that is meant to be stable and is not intented to change. This header file should be included in [PD externals](https://github.com/pure-data/externals-howto). 

### m_imp.h

This file includes _details_ of the PD implementation that are **subject to change**. You will notice it includes the important [`struct _class`](https://github.com/pure-data/pure-data/blob/7c27aa0ad505bb4802eee3fc40886836c814353f/src/m_imp.h#L31) and mostly it includes a lot of `extern` declarations. Notice also those extern declarations are acompanied by a comment saying in which file those functions are located.

## m_pd.h vs m_imp.h

So notice that **m_pd.h** serves as a "general purpose" header file for stuff in PD that you can use "safely" and that it is unlikely that it will feature some code breaking change whie **m_imp.h** is also a "general purpose" header file that includes externs from a lot of the pd's C files (.c) that are really details of implementation and that could change at any release, breaking your code.

One interesting way to see how this is put together is to notice that the important [`_class struct`](https://github.com/pure-data/pure-data/blob/7c27aa0ad505bb4802eee3fc40886836c814353f/src/m_imp.h#L31) is given an extern declaration in **m_pd.h** so you can reference it, but since it's definition is in **m_imp.h**, unless you also include _m_imp.h_ in your file you really can't access it's members. Also, it has a typedef aliasing it to `*t_pd`, so henceforth the public api will know it by that name. **m_pd.h** only uses `t_class` in the extern declarations.

```C
189: EXTERN_STRUCT _class;
190: #define t_class struct _class

...

213: typedef t_class *t_pd; /* pure datum: nothing but a class pointer */
```

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

and also [m_pd.h line 249](https://github.com/pure-data/pure-data/blob/7c27aa0ad505bb4802eee3fc40886836c814353f/src/m_pd.h#L249)

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

It seens that glists are used for abstractions and t_canvas are used for toplevel patches?!?!

```C
struct _glist
{
    t_object gl_obj;            /* header in case we're a glist */         <--------------
    t_gobj *gl_list;            /* the actual data */
   [...]
   [...]
```

notice the comment. Also see this function ([g_graph.c](https://github.com/pure-data/pure-data/blob/7c27aa0ad505bb4802eee3fc40886836c814353f/src/g_graph.c#L192))

```C
t_canvas *glist_getcanvas(t_glist *x)
{
    while (x->gl_owner && !x->gl_havewindow && x->gl_isgraph)
            x = x->gl_owner;
    return((t_canvas *)x);
}
```

Also see how [Else/args](https://github.com/porres/pd-else/blob/24fa7d93783dec76be7f8b947c5aad7682e98292/classes/source/args.c#L37) gets the arguments of an abstraction

Moreover in [g_editor.c](https://github.com/HenriAugusto/pure-data/blob/master/src/g_editor.c) we this two methods

[`static void canvas_selectall(t_canvas *x);`](https://github.com/HenriAugusto/pure-data/blob/fae7fab842e989b4b8ce85807a0c7cf1ad99350e/src/g_editor.c#L3750)

[`void glist_selectall(t_glist *x);`](https://github.com/HenriAugusto/pure-data/blob/fae7fab842e989b4b8ce85807a0c7cf1ad99350e/src/g_editor.c#L305)

```C
static void canvas_selectall(t_canvas *x)
{
    t_gobj *y;
    if (!x->gl_editor)
        return;
    if (!x->gl_edit)
        canvas_editmode(x, 1);
        /* if everyone is already selected deselect everyone */
    if (!glist_selectionindex(x, 0, 0))
        glist_noselect(x);
    else for (y = x->gl_list; y; y = y->g_next)
         {
             if (!glist_isselected(x, y))
                 glist_select(x, y);
         }
}
```

```C
void glist_selectall(t_glist *x)
{
    if (x->gl_editor)
    {
        glist_noselect(x);
        if (x->gl_list)
        {
            t_selection *sel = (t_selection *)getbytes(sizeof(*sel));
            t_gobj *y = x->gl_list;
            x->gl_editor->e_selection = sel;
            sel->sel_what = y;
            gobj_select(y, x, 1);
            while ((y = y->g_next))
            {
                t_selection *sel2 = (t_selection *)getbytes(sizeof(*sel2));
                sel->sel_next = sel2;
                sel = sel2;
                sel->sel_what = y;
                gobj_select(y, x, 1);
            }
            sel->sel_next = 0;
        }
    }
}
```

### The editor

PD instantiates a [`t_editor`](https://github.com/pure-data/pure-data/blob/7c27aa0ad505bb4802eee3fc40886836c814353f/src/g_canvas.h#L92) each `g_list` becomes visible, i.e. when it's window is open (not necessarily literaly visible. it can be minimized).

Picture the following: you have a toplevel patch called **synth.pd** and inside it you have a subpatch **[pd stuff]** and an abstractions **[crazyfilter]**.

Let's say you open that patch from the menu and now you have only one patch window, the `synth.pd` one. There is an `t_editor` for that window but there **isn't** an `t_editor` for **[crazyfilter]** because since the abstraction is closed there is no point in having one consuming memory (and using cpu time upon loading) if that abstraction is closed. Only the stuff regarding it's implementation/logic should be in memory. The same goes for **[pd stuff]**. As soon as you open your subpatch or your abstraction a `t_editor` would be instantiated for each one of those windows. Similarly, as soon as you close them the corresponding `t_editor` would be destroyed.

See [`canvas_create_editor(t_glist *x)`](https://github.com/pure-data/pure-data/blob/7c27aa0ad505bb4802eee3fc40886836c814353f/src/g_editor.c#L1917)


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
