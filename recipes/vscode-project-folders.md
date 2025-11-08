# VSCode Project Folders

When you have a project which consists of multiple git repositories, for example because 
the "system" you develop is comprised of different repositories, for example separate codebases
for backend and frontend.

Put the main directory under version control with `git init`.

Create a `.gitignore` like this

```
*
!.claude/
!.claude/skills/
!.claude/skills/**
!.claude/settings.json
```

This will make sure all other repos under the main dir are not part of the main repo.

Create a VSCode project and 

1. add the top directory, 
2. as well as all other directories of the other repositories, one by one.

Store the project file in the top directory.

Claude Code VSCode plugin will use as its working dir the first listed folder of the sidebar explorer.
