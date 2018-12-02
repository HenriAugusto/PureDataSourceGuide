# t_rtext

The [`t_rtext`](https://github.com/pure-data/pure-data/blob/7c27aa0ad505bb4802eee3fc40886836c814353f/src/g_rtext.c#L27) structure is used for handling what the user types in the object boxes. Like when you create an object box for example and you type [print].


```C
struct _rtext
{
    char *x_buf;    /*-- raw byte string, assumed UTF-8 encoded (moo) --*/
    int x_bufsize;  /*-- byte length --*/
    int x_selstart; /*-- byte offset --*/
    int x_selend;   /*-- byte offset --*/
    int x_active;
    int x_dragfrom;
    int x_height;
    int x_drawnwidth;
    int x_drawnheight;
    t_text *x_text;
    t_glist *x_glist;
    char x_tag[50];
    struct _rtext *x_next;
};
```
