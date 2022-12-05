# Brace Expansion

## Sequence expressions

Try the following:  
- **echo {a..z}**  
(from a to z)
- **echo {a..z..4}**  
(from a to z in jumps of 4 letters)
- **echo  {2..479}**  
(from 2 to 479)
- **echo  {02..0479}**  
(from 2 to 479, but force each element to have the same length)
- **echo  {2..479..10}**  
(from 2 to 470 in jumps of 10)

## Brace Expansion

Try the following:  
- **echo X{a..z}Y**
- Try other versions of sequnce expressions with preamble and postcript
- **echo X{a,b,c}Y**
- Create 3 directories:  
**mkdir dir-{name,height,width}s**

##  Nested

Nested brace expressions are concatenated.  
Try:  
- **echo X{{ab,cd,ef},{f..j}, u5,v6,w18}Y**
- Try recursive expansions:  
**echo {1..5}{a..c}**  
or even this:  
echo {1..5}{a..c}{t,u}