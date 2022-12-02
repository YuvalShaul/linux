# IFS explained

## Showing the value of IFS

- First, try to print the IFS variable like this:  
**printf "%s" $IFS**
- It does not work because the output is parsed using the IFS, so IFS characters are extracted from the output.  
So try this instead:  
**printf "%s" "%IFS"**  
(you can read more about the [shell parameter expansion](https://www.gnu.org/software/bash/manual/bash.html#Shell-Parameter-Expansion))
- This is better, but you cannot still be sure what you see. So try this:  
**echo "$IFS" | hexdump -C**  
Can you explain what you see?

## Playing with IFS

- Save the folowing script and try it with different mixtures of IFS characters:
```
IFS=$1

tmpdata="aaa,bbb,ccc,ddd,eee"

for word in $tmpdata
do
  printf "%s\n" $word
done
```
- The text data is seperated by commas, instead of spaces, so you have to include a comma in your IFS to make parsing work:  
**ifsdemo ':,%'