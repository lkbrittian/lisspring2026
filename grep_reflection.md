# Grep Introduction Reflection - 03/07/2026

## Overview
This file is designed to record grep commands used to practice with grep and how they work using an example .bib file.

## Environment
- VM Provider: Google Cloud
- OS: Ubuntu 24.04 LTS x86/64

## Commands Tested

### Part A 

1. 

```
grep "Journal" savedrecs.bib
```

Will return every line with "Journal" in it from the file savedrecs.bib. Search is case sensitive.

2. 

```
grep -i "journal" savedrecs.bib
```

Will return every line with "journal" regardless of capitalization from the .bib file.

3. 

```
grep -vi "journal" savedrecs.bib
```

The command -v will have the search return with every line that does NOT match the search term. Combining it with "i" for "-vi" will have it ignore capitilzation as well, so it will return every line that does not have any version of "journal" in it.

4. 

```
^
```

A regex that refers to the start of a line. When used in a grep search command, it tells the search to only look for the search term at the start of the line. When used like this -

```
grep -vi "^journal" savedrecs.bib
```

-it will only exclude lines that start with journal, leaving any lines that may reference a title or name in the search results.

5. 

```
$
```

A regrex that refers to the end of the line. Like the prior regrex, it can be used to filter out search terms that appear at the end of the line.

6. 

```
grep -ic "journal" savedrecs.bib
```

"-c" will return a count of the number of lines that have the search term in it.

7. 

```
grep -vic "journals" savedrecs.bib
```

-vic will return a count of lines that do not have the search term in it.

8. 

 
```
grep -Ei "(journal|author)" savedrecs.bib
```

Acts as a Boolean search, looking for lines that have journal OR author contained within. Can be used with more than two search terms.

9. 

```
 grep -iw "journal" savedrecs.bib
```

-w Will only return lines where journal is a complete word - would not return lines that have "journals" or "journalistic"

### Part B - Lines

1. -A NUM

```
grep -i "journal" -A2 savedrecs.bib
```

Will return lines containing the search term as well as the following two lines

2. -B NUM

```
grep -i "journal" -B2 savedrecs.bib
```

Will return lines containing the search term as well as the prior two lines

3. -C NUM

```
grep -i -C1 "journal" savedrecs.bib
```

Returns the lines with the search terms and the line before and line after those lines.

4. -m NUM

```
grep -i -m1 "journal" savedrecs.bib
```

Will only return the first hit for the given search term

5. -n

```
grep -in "journal" savedrecs.bib
```

Will give the line number of each hit

### Part C - Character Matching

When searching for character strings rather than words, it may be useful to search for strings that fit a certain criteria.

```
grep -Eiw "[a-z]{5}" savedrecs.bib
```

a-z tells the command to search for characters between a and z, and 5 refers to how many characters in the "word". -w modifies the command to look for whole words.


This can also be used for numbers, which would be useful for looking for a range of years.

```
grep -Eiw "[0-9]{4}" savedrecs.bib
```

We can even specify what letters we want the word/string to start and end with and what is in between.

```
grep -Eiw "j.{5}l" savedrecs.bib
```

Would return lines that contain a word that starts with j, ends with l, and have 5 letters in between.

## Reflection

Walking through each command and writing it out with a new example and explanation of what it does really helped me understand them all better. The first time we went through this, the character matching commands confused me, but I now feel I have a better grasp on how to use them and what situations they would be useful in.
