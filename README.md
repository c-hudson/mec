# mec
   This script takes mushcode and attempts to make it more readable
   by splitting single lines across multiple lines. Various arbitrary
   rules are used to split up commands and functions.

   Conversion back into a MUSH appropriate form is supported and is
   done so without loss of characters from the original. I.e. If the
   mushcode is "formatted" and then "unformatted", the output should be
   the same as the original mushcode.

   This script also supports converting formated text into its mush
   equalivant. Spaces, returns, and special characters will be converted
   into %b, %r, or into whatever is needed to be displayed. To invoke
   this mode, place a "@@ mushify" on the blank line before the
   text to be converted. This can be turned off with '@@ mushify off'

   The script supports the reverse process of converting the %b, %r, 
   etc to its more readable form.

   Example:
```
      @@ mushify
      @desc me=
          _-----_
        .'__     `.
        |/  \_~~'\|
        | _  _  _ |
       (|'o`'|`'o`|)
        \`-' | `-'/
         `. --- .'
       ___|`---'|___
      @@ mushify off
```
   Becomes:
```
      @@ mushify
      @desc me= %b %b_-----_ %b.'__ %b %b `. %b|/ %b[chr(92)]_~~'[chr(92)]| 
      %b| _ %b_ %b_ | (|'o`'|`'o`|) %b[chr(92)]`-' | `-'/ %b `. --- .' ___|
      `---'|___
      @@ mushify off
```
   Note: Returns were added to the output for readability.
      

```
   Usage: ./mec.pl [<options>] <filename>

      Options:

      --unformat     : Unformat MushCode into mush readable format.
      --format       : Format MushCode into multiple lines for readability
```
Arbitrary Rules:
   o  Do not split up short strings/functions. Code that spans several
         pages that doesn't need to is unreadable too.
   o  Wrap long strings of text.
   o  Spaces at the end of a line are significant after formatting.
   o  Spaces at the beginning of a line are not significant after formatting.
   o  Make sure formatted text can be unformatted back to the original
      without modification.
   o  Functions arguments will be split and indented.

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
         readable. Commands currently with special formatting are:
         @switch/@select, @dolist, &, @while, think, and @pemit

      Example with @dolist:        [return added in example for readability]

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
