# Index

- <a id="index-api">[The API](#the-api)</a>
   - <a id="index-m_pdh">[m_pd.h](#m_pdh)</a>
   - <a id="index-m_imph">[m_imp.h](#m_imph)</a>
   - <a id="index-m_pdh-vs-m_imph">[m_pd.h vs m_imp.h](#m_pdh-vs-m_imph)</a>
   - <a id="index-g_canvash">[g_canvas.h](#g_canvash)</a>

Back to the [**main index**](https://github.com/HenriAugusto/my-pure-data-source-studies/blob/master/README.md#index-api)

# [The API](#index-api)

The first thing to notice is that there aren't much header files (.h) . The ones in the `/src` directory are
g_all_guis.h, g_canvas.h, g_undo.h, m_imp.h, m_pd.h, s_audio_alsa.h, s_audio_paring.h, s_stuff.h, s_utf8.h, x_vexp.h.

The main ones are

- **m_pd.h**
- **m_imp.h**
- **g_canvas.h**

## [m_pd.h](#index-m_pdh)

This file inclues the **public** API. That is, stuff that is meant to be stable and is not intented to change. This header file should be included in [PD externals](https://github.com/pure-data/externals-howto). 

## [m_imp.h](#index-m_imph)

This file includes _details_ of the PD implementation that are **subject to change**. You will notice it includes the important [`struct _class`](https://github.com/pure-data/pure-data/blob/7c27aa0ad505bb4802eee3fc40886836c814353f/src/m_imp.h#L31) and mostly it includes a lot of `extern` declarations. Notice also those extern declarations are acompanied by a comment saying in which file those functions are located.

## [m_pd.h vs m_imp.h](#index-m_pdh-vs-mimph)

So notice that **m_pd.h** serves as a "general purpose" header file for stuff in PD that you can use "safely" and that it is unlikely that it will feature some code breaking change whie **m_imp.h** is also a "general purpose" header file that includes externs from a lot of the pd's C files (.c) that are really details of implementation and that could change at any release, breaking your code.

One interesting way to see how this is put together is to notice that the important [`_class struct`](https://github.com/pure-data/pure-data/blob/7c27aa0ad505bb4802eee3fc40886836c814353f/src/m_imp.h#L31) is given an extern declaration in **m_pd.h** so you can reference it, but since it's definition is in **m_imp.h**, unless you also include _m_imp.h_ in your file you really can't access it's members. Also, it has a typedef aliasing it to `*t_pd`, so henceforth the public api will know it by that name. **m_pd.h** only uses `t_class` in the extern declarations.

```C
189: EXTERN_STRUCT _class;
190: #define t_class struct _class

...

213: typedef t_class *t_pd; /* pure datum: nothing but a class pointer */
```
## [g_canvas.h](#index-g_canvash)

As the note in the file states, it is private API.

> /* NOTE: this file describes Pd implementation details which may change
> in future releases.  The public (stable) API is in m_pd.h. */
