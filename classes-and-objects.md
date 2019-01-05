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

Still i can't figure what `t_pd` is. **Let's find out.**

[`t_pd` is an alias for `t_class`](https://github.com/pure-data/pure-data/blob/7c27aa0ad505bb4802eee3fc40886836c814353f/src/m_pd.h#L213)

```C
typedef t_class *t_pd; /* pure datum: nothing but a class pointer */
```

Here is the [definition of `t_class`](https://github.com/pure-data/pure-data/blob/7c27aa0ad505bb4802eee3fc40886836c814353f/src/m_imp.h#L31)

```C
struct _class
{
    t_symbol *c_name;                   /* name (mostly for error reporting) */
    t_symbol *c_helpname;               /* name of help file */
    t_symbol *c_externdir;              /* directory extern was loaded from */
    size_t c_size;                      /* size of an instance */
#ifdef PDINSTANCE
    t_methodentry **c_methods;          /* methods other than bang, etc below */
#else
    t_methodentry *c_methods;
#endif
    int c_nmethod;                      /* number of methods */
    t_method c_freemethod;              /* function to call before freeing */
    t_bangmethod c_bangmethod;          /* common methods */
    t_pointermethod c_pointermethod;
    t_floatmethod c_floatmethod;
    t_symbolmethod c_symbolmethod;
    t_listmethod c_listmethod;
    t_anymethod c_anymethod;
    const struct _widgetbehavior *c_wb; /* "gobjs" only */
    const struct _parentwidgetbehavior *c_pwb;/* widget behavior in parent */
    t_savefn c_savefn;                  /* function to call when saving */
    t_propertiesfn c_propertiesfn;      /* function to start prop dialog */
    struct _class *c_next;
    int c_floatsignalin;                /* onset to float for signal input */
    char c_gobj;                        /* true if is a gobj */
    char c_patchable;                   /* true if we have a t_object header */
    char c_firstin;                 /* if patchable, true if draw first inlet */
    char c_drawcommand;             /* a drawing command for a template */
};
```
