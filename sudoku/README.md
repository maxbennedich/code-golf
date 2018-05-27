## 235 bytes: Sudoku solver

```c#
string S(string s){var c='0';int y=0,x=s.IndexOf(c);string q="",t=s;if(x>=0){for
(t=q;y<81;++y)q+=(x-y)%9*(x/9^y/9)*(x/27^y/27|x%9/3^y%9/3)==0?s[y]:c;for(;++c<58
;)t+=q.Contains(c+"")?"":S(s.Substring(0,x)+c+s.Substring(x+1));}return t;}
```

Inspired by [this thread](https://codegolf.stackexchange.com/questions/378/implement-a-brute-force-sudoku-solver).

Sample use:

```c#
var @in =
    "030001000"+
    "006000050"+
    "500000983"+
    "080006302"+
    "000050000"+
    "903800060"+
    "714000009"+
    "020000800"+
    "000400030";

string F(string s){var o="\n";for(int n=-1,i=11;i<144;)o+=++i%12<
1?'\n':i%4<1?'|':i/12%4<1?'-':s[++n]<49?' ':s[n];return o+"\n";}

var sw = Stopwatch.StartNew();

var @out = S(@in);

Console.WriteLine($"INPUT:{F(@in)}SOLUTION:{F(@out)}solved in {sw.ElapsedMilliseconds} ms");
```

Output:

```
INPUT:

 3 |  1|
  6|   | 5
5  |   |983
---|---|---
 8 |  6|3 2
   | 5 |
9 3|8  | 6
---|---|---
714|   |  9
 2 |   |8
   |4  | 3

SOLUTION:

832|591|674
496|387|251
571|264|983
---|---|---
185|746|392
267|953|418
943|812|765
---|---|---
714|638|529
329|175|846
658|429|137

solved in 259 ms
```

Explained:

```c#
string S(string s)
{
    var c = '0';

    int
        y = 0,
        x = s.IndexOf(c); // index of first unknown digit; this is the digit we're solving for

    string
        q = "", // invalid digits for index 'x' will be stored here (will contain duplicates)
        t = s; // return value; initialized to solution

    // keep recursing while any unknown digits remain
    if (x >= 0)
    {
        // create set of invalid digits by looping over all currently populated digits
        for (t = q; y < 81; ++y)
            q +=
                (x - y)%9 * // exclude digits in the same column
                (x/9 ^ y/9) * // exclude digits in the same row
                (x/27 ^ y/27 | x%9/3 ^ y%9/3) // exclude digits in the same 3x3 box
                == 0 ? s[y] : c;

        // now recurse by trying every valid digit in index 'x'
        for (; ++c < 58; )
            t += q.Contains(c+"") ? "" : S(s.Substring(0, x) + c + s.Substring(x+1));
    }

    // 't' will either be empty if no solution was found, or contain the solution
    return t;
}
```