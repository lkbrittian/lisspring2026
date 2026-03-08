# Updating Old Entries - 03/07/2026

## Overview
This document records my process at uploading proper Markdown files for the two entries in my repository. 
The goal was to change the file typ and commit those changes to my GitHub repository.
This document also serves as practice for the proper documentation style before trying to document Module 3 work.

## Environment
- VM Provider: Google Cloud
- OS: Ubuntu 24.04 LTS x86/64


## Steps for Completion
1. Upon opening the VM, I checked the files in my directory, then navigated to the local respository to find my previous two entries.
2. Using mv, I renamed entry01 and entry02 to entry01.md and entry02.md
```mv entry01 "entry01.md"
mv entry02 "entry02.md"```

3. I then commited the changes to GitHub and pushed them to the remote repository

```git add "entry01.md"
git commit -m "entry01.md"
git push origin main```

```git add entry02.md
git commit -m "entry02.md"
git push origin main```

## Results
Rather than renaming the files in the remote repository, I now have files both with and without the .md extension. As the original files weren't written in Markdown, I'm not sure if the .md extension ensured that the new files are in Markdown.
I deleted the original files directly from the remote repository, so now the .md entries are the only ones available.

## Reflection
While renaming and commiting the changes went just fine, it didn't work the way I thought it would in renaming the files in the local repository and then committing that change.
I don't know if simply deleting the old files after getting a duplicate counts or not, but I did learn that to delete on the remote repository, you have to commit to that change too.
I suppose the main takeaway should be to do it right the first time, but I don't think I'm the only one that didn't realize documentation was mandatory. 
I will do a better job moving forward.

EDIT: Deleting those files seems to have caused branches - I about had a heart attack from the error messages. Thankfully, I think I've resolved it? I was able to push this file to the remote repo at any rate, though something went wrong with the formatting after the first code block. Working on fixing it now.
