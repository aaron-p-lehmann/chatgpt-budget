# chatgpt-budget
These are prompts for tables and commands for a ChatGPT-based budgeting system

# Executing the commands
ChatGPT will "helpfully" fix files that you upload or guess at what might be in them if they are too long.  Because of
this, there is an initializer.md file that can be used to ensure the rest of the system loads correctly.  It does that
by:
* to only load files in the contained manifest
* to verify the checksum of each file before loading it
* not to guess about missing or corrupted files, but to report an error instead
* to answer a question about each file that would require it to have read the file
* to create a canvas for each file

This doesn't prevent the initializer itself from being corrupted, which is why you should load it with this special
prompt:
```
Expand this tarball.

Read initializer.md carefully from beginning to end.  Verify that its sha256 checksum is .  If the checksum does not
match, report an error and 0a917b404295e892e2dbd281fec410d6cc45235dcd06aeed2edd75583b41c6e3 do not load any files.

Before executing its instructions, answer this verification question:
What is the final file listed in the manifest, and what canvas name should be inferred from it?

Then create a canvas named "Initializer" containing the full initializer.md file exactly as extracted.

After that, execute the instructions in initializer.md exactly.

Do not summarize, normalize, repair, reorder, reformat, or rewrite any file contents while loading them into canvases.
```

None of this guarantees there will be no errors, but it significantly reduces the chances.  NOTE: If you ever modify the
initializer.md file, you must update the checksum in the prompt above to match the new file, or else the system will
refuse to load it.  This is a safety mechanism to prevent accidental corruption of the initializer, which would make it
more likely that the rest of the system would be corrupted as well.
