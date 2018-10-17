# my-pure-data-source-studies
This is some notes i wrote while studying PD's source. Right know it's NOT intended to be something read by others but in the future i have plans to suggest a development guide

## Classes and Objects

PD is written in C so there is no classes like you would find in C++ or Java for example.

PD's graphical objects are named **t_gobj** (m_pd.h)

## glist

/* this file defines the structure for "glists" and related structures and
functions.  "Glists" and "canvases" and "graphs" used to be different
structures until being unified in version 0.35.

/* the t_glist structure, which describes a list of elements that live on an area of a window

(g_canvas.h)[https://github.com/pure-data/pure-data/blob/master/src/g_canvas.h] line 154
