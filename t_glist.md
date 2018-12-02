## [glist](https://github.com/HenriAugusto/my-pure-data-source-studies/blob/master/README.md#index-glist)

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

but **note that the `glist_selectall` is not used** anywhere in the code.

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

#### note

in the starting coments on g_canvas.h there is this

> A glist that's just a text object on its parent is always "toplevel."  An
> embedded glist can switch back and forth to appear as a toplevel by double-
> clicking on it.  Single-clicking a text box makes the toplevel become visible
> and raises the window it's in.

**But i don't see any difference between single or double clicking an abstraction or a sublist!**

### [t_editor](#index-t_editor)

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
