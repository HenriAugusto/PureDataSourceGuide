## [Classes and Objects](https://github.com/HenriAugusto/my-pure-data-source-studies#index-classes-and-objects)

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
