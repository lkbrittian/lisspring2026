# yaz-client Documentation - 03/09/2026

## Overview 

This documents my experience downloading yaz-client and using it to connect with library databases and conduct searches.

## Environment
- VM Provider: Google Cloud
- OS: Ubuntu 24.04 LTS x86/64

## Process

### Downloading yaz-client

```
apt search yaz
```

I first searched for yaz client by searching for the software.

```
apt show yaz
```

Using apt show, I verified that yaz was the correct package for what I wanted to install.

```
sudo apt install yaz
```

Finally, using sudo apt install, I installed yaz-client to the virtual machine. There were no issues encountered in this step.

### Using yaz

```
yaz-client
```

I was able to open yaz with no issues and then connected to the University of Kentucky library catalog with the provided link.

```
open saalck-uky.alma.exlibrisgroup.com:1921/01SAA_UKY
```

I first ran the command from our text as a first search. 

```
find @and @attr 1=4 "information" @attr 1=21 "library science"
```

Each part of the command works to build a search the way one would search a database.

Find = the command to search
@and = AND boolean search
@attr = attribute, the metadata field to search
"" = keyword to search

1=4 - Title
1=21 - Subject Heading
1=1 - Personal name
1=1016 - Any

```
show 1
show 2
```

To show the first record, show the second record, etc etc

To practice searching with yaz, I ran a few commands similar to the example command with different search terms and a different boolean operator.

```
find @and @attr 1=4 "panda" @attr 1=21 "ecology"
find @or @attr 1=4 "panda" @attr 1=4 "polar bear"
```

To exit yaz, simply use the quit command.

```
quit
```

### Saving records

Bibliographic records brought up with yaz-client can be sazed to a file using the command:

```
yaz-client -m records.marc
```

When a search is ran and records are shown, those records will be saved to the file and can be accessed after exiting yaz.

```
Z> open saalck-uky.alma.exlibrisgroup.com:1921/01SAA_UKY
Z> find @and @attr 1=4 "information" @attr 1=21 "library science"
Z> show 1
Z> show 2
Z> show 3
Z> quit
```

After quitting, the file can be accessed - however, the default file type isn't very readable. Attempting to read it results in a massive wall of text that isn't very reader-friendly.
```
head records.marc
```

 Converting it from a .marc file to a .json file will make it easier to query for practical use.

```
yaz-marcdump -o json records.marc > records.json
jq . records.json > records-formatted.json
```

After formatting to .json, I did try to run the less command to read it - I can't say I found the results any easier to read than the .marc file, though. I then ran the example commands for extracting specific fields from the dataset:

```
jq '.fields[] | select(has("650")) | .["650"].subfields[] | select(has("a")) | .a' records-formatted.json
jq '.fields[] | select(has("650")) | .["650"].subfields[] | select(has("x")) | .x' records-formatted.json | sort | uniq -c | sort
```

While the commands are confusing and I'm not sure how I would go about constructing my own, the results the commands disaplyed were far easier to read and understand, making it easier to examine aspects of the records we pulled from the yaz-client search.

```
"Librarians"
"Library employees"
"Assertiveness (Psychology)"
"Library science"
"Public services (Libraries)."
"Library science"
"Library science"
"Information science"
"Libraries and society."
"Libraries"
```

```
  1 "Aims and objectives."
      1 "Technological innovations."
      2 "Psychology."
      3 "Philosophy."
```


## Reflection

While searching with yaz is clunky compared to using a GUI-based search bar in a database, being able to write out the search commands and then see the bibliographic record as usually seen from the backend helped me gain an appreciation for how searching databases works on the graphical side of things. I was working on my exit assesment paper when we were originally installing and using yaz, and refreshing myself on MARC and then being able to connect that to how yaz searches databases and displays results was a very fun moment - and something I wanted to include in my paper but couldn't quite find a good way to do.
Searching with yaz went fine for me, but although I like being able to record the results as we go and save them to a file to search through later, I found that section difficult to understand exactly what it was that I was doing. I was able to produce the expected results, but that was more through copy-paste than actually understanding the commands being used to filter and present the selected fields. I would need more practice with the jq commands before being able to say I'm comfortable with that aspect of yaz.
