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

   Output from commands can be converted into its mush equalivant as well
   with the --one or --multi command line options. This makes it useful for
   quoting output from a command directly to the mush. See example below.
 

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

   --one and --multi example:

```
   > who | ./mec.pl --one
   %radrick %b tty1[space(9)]2021-01-14 16:59%radrick %b tty7[space(9)]2021-01-06 20:51 (:0)%radrick %b pts/2[space(8)]2021-01-14 19:10 (192.168.1.1)%radrick %b pts/1[space(8)]2020-12-19 00:18 (tmux(25724).\%2)%radrick %b pts/0[space(8)]2021-01-08 09:08 (tmux(25724).\%7)%radrick %b pts/4[space(8)]2021-01-10 23:31 (tmux(25724).\%8)%radrick %b pts/5[space(8)]2020-12-18 23:39 (tmux(25724).\%0)%radrick %b pts/6[space(8)]2020-12-19 00:18 (tmux(25724).\%3)%radrick %b pts/9[space(8)]2020-12-21 19:00 (tmux(25724).\%4)%radrick %b pts/10[space(7)]2021-01-11 10:22 (tmux(25724).\%9)%radrick %b pts/11[space(7)]2020-12-26 20:29 (tmux(25724).\%6)%radrick%b %bpts/13[space(7)]2021-01-11 13:24 (tmux(25724).\%10)

   > who | ./mec.pl --multi
   adrick %b tty1[space(9)]2021-01-14 16:59
   adrick %b tty7[space(9)]2021-01-06 20:51 (:0)
   adrick %b pts/2[space(8)]2021-01-14 19:10 (192.168.1.1)
   adrick %b pts/1[space(8)]2020-12-19 00:18 (tmux(25724).\%2)
   adrick %b pts/0[space(8)]2021-01-08 09:08 (tmux(25724).\%7)
   adrick %b pts/4[space(8)]2021-01-10 23:31 (tmux(25724).\%8)
   adrick %b pts/5[space(8)]2020-12-18 23:39 (tmux(25724).\%0)
   adrick %b pts/6[space(8)]2020-12-19 00:18 (tmux(25724).\%3)
   adrick %b pts/9[space(8)]2020-12-21 19:00 (tmux(25724).\%4)
   adrick %b pts/10[space(7)]2021-01-11 10:22 (tmux(25724).\%9)
   adrick %b pts/11[space(7)]2020-12-26 20:29 (tmux(25724).\%6)
   adrick %b pts/13[space(7)]2021-01-11 13:24 (tmux(25724).\%10)

   An example use of --one that could be used with tinyfugue:

      /quote -0 say !who | ./mec --one

   An example use of --multi that could be used with tinyfugue:

      /quote -0 :|> !who | ./mec --multi

   Usage: ./mec.pl [<options>] <filename>

      Options:

      --unformat     : Unformat MushCode into mush readable format.
      --format       : Format MushCode into multiple lines for readability
      --one          : Mushify input for using in a say command
      --multi        : Mushify input for quoting in multipe lines


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
