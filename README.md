# mec
   This script takes mushcode and attempts to make it more readable
   by splitting single lines across multiple lines. Various arbitrary
   rules are used to split up commands and functions.

   Conversion back into a MUSH appropriate form is supported and is
   done so without loss of characters from the original. I.e. If the
   mushcode is "formated" and then "unformated", the output should be
   the same as the original mushcode.

   Command line usage may be optained by running this script without any
   arguements.

   
```
   Arbitrary Rules:
   o  Do not split up short strings/functions. Code that spans several
         pages that doesn't need to is unreadable too.
   o  Wrap long strings of text.
   o  Spaces at the end of a line are significant after formating.
   o  Spaces at the begining of a line are not significant after formating.
   o  Make sure formated text can be unformated back to the original
      without modification.
   o  Functions arguements will be split and indented.

      The Arguments of the function will be indented so that it lines up with
        the end of the function name on the next line.

      Example:                      [return added in example for readability]
         &match  com=[first([extract(u(gdb),match(u(gdb),first(%0)),1)] 
            [extract(u(gdb),match(u(gdb),*[first(%0)]*),1)])]

      Becomes: 
         &match  com=
            [first([extract(u(gdb),
                            match(u(gdb),first(%0)),
                            1
                   )]
                   [extract(u(gdb),
                            match(u(gdb),*[first(%0)]*),
                            1
                   )]
            )]
      
   o  Some commands will be indented in a way that I think is more
         readable. Commands currently with special formating are:
         @switch/@select, @dolist, &, @while, think, and @pemit

      Example:                      [return added in example for readability]

         &short com=$=*:@dolist [u(lwho,u(first))]={@pemit before(##,_)=
            [u(format,%#,##,%0)]};@pemit %#=[switch(u(first),,v(msg_none),
            MONITOR,v(msg_monitor))]

      Becomes:

         &short com=$=*:
            @dolist [u(lwho,u(first))]=
            {
               @pemit before(##,_)=
                  [u(format,%#,##,%0)]
            };
            @pemit %#=
               [switch(u(first),,v(msg_none),MONITOR,v(msg_monitor))]

```
