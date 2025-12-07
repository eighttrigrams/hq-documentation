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

The search function of VSCode, in combination with the gitignores, depends on the repositories being added individually.

Claude Code VSCode plugin will use as its working dir the first listed folder of the sidebar explorer.

## Claude

Note that Claude native plugin takes first directory.

## Clojure-LSP automatically set on

It is important for some reason that Clojure LSP via Calva detects `frontend` here
and `backend`, but not the `project` repo.

- project/ # project dir, also a git repo
- project/project.code-workspace # vscode project workspace
- project/frontend 
- project/backend

```json
{
	"folders": [
		{
			"path": ".",
			"settings": {
				"calva.autoEvaluateCode.onSave.enabled": false,
				"calva.autoConnect": true,
				"formatOnSave.enabled": false
			}
		},
		{
			"path": "frontend",
			"settings": {
				"calva.autoEvaluateCode.onSave.enabled": false,
				"calva.autoConnect": true,
				"formatOnSave.enabled": true,
				"formatOnSave.languages": ["clojure"]
			}
		},
		{
			"path": "backend",
			"settings": {
				"calva.autoEvaluateCode.onSave.enabled": false,
				"calva.autoConnect": true,
				"formatOnSave.enabled": true,
				"formatOnSave.languages": ["clojure"]
			}
		}
	],
	"settings": {
		"calva.enableClojureLspOnStart": "when-workspace-opened-use-workspace-root",
		"calva.projectRootsSearchExclude": [
			"other-repo",
			"*.bkp",
			"node_modules",
			".git",
			".playwright-mcp"
		]
	}
}
```

Note that moving directories makes one loose these settings.
