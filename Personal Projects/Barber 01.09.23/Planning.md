# Glossary
- **Collection**: A folder with snippets and folders. It is defined by a collection config file and collection metadata. The order of precedence used to select the collection is an explicitly defined collection/alias, then the working directory. If no collection is found the operation will not complete.
- **Snippet**: Any text-based file stored within a collection. When defining or listing a snippet it is always in the form of a relative path to that snippet from the root of the collection being used.
- **Alias**: A collection alias is a unique string beginning with `~` character which relates to a single collection. They are used defined and stored within the app metadata. If both an alias and collection path are provided to a command, only the collection path will be used.
- **Config**: There are two types of config:
	- **App**: The app config stores:
		- The mappings for all aliases and their collections path (relative to users home).
		- Default config for the TUI. This is overridden if the same values exist in the collections config.
	- **Collection**: The collection config stores:
		- Overrides for the TUI configuration.
- **Metadata**: There are two types of metadata:
	- **App**: Stores a list of aliases in the form of a mapping between the alias value and the collection path.
	- **Collection**: Stores a list of snippets, each with:
		- A description.
		- A set of tags.
		- A path, relative to the root of the collection.
- **Initialise Collection**: This includes the following steps:
	- Initialise a new git repository if one does not exist in the current directory.
	- Create an empty `.barber.yaml` metadata file for the collection.

# CLI
- `barber`: Start interactive mode in the given collection.
	- *--collection, -c* (string): The path to a collection, or a collection alias with the prefix.
	- `snippet`, `snpt`: Display subcommands.
		- `remove`, `rm`: Remove the specified file from the collection metadata.
			- **path** (string, positional, required): The file to remove. Relative to the root of the collection.
			- *--collection, -c* (string): The path to a collection, or a collection alias with the prefix.
		- `open`, `o`: Open the specified snippet in the users preferred editor.
			- **path** (string, positional, required): The file to open. Relative to the root of the collection.
			- *--collection, -c* (string): The path to a collection, or a collection alias with the prefix.
		- `move`, `mv`: Moves the snippet from the old path to the new path.
			- **old** (string, positional, required): The old file path. Relative to the root of the collection.
			- **new** (string, positional, required): The new file path. Relative to the root of the collection.
			- *--collection, -c* (string): The path to a collection, or a collection alias with the prefix.
		- `add`, `a`: Add a snippet entry to the metadata for an existing file.
			- **path** (string, positional, required): The file to add to the metadata. Relative to the root of the collection.
			- *--collection, -c* (string): The path to a collection, or a collection alias with the prefix.
	- `list`, `ls`: List all snippets in the collection.
		- *--collection, -c* (string): The path to a collection, or a collection alias with the prefix.
	- `scan`: Goes through all files & directories to update metadata. Attempts to carry metadata along renames & moves by comparing using `git diff` (look into other options).
		- *--collection, -c* (string): The path to a collection, or a collection alias with the prefix.
		- *--keep* (bool, default false): Whether or not to keep the metadata for snippets thought to be deleted. This allows manual alterations if the operation fails to detect renames or moves. NOTE: This can result in orphaned snippet metadata which can waste storage and will be deleted when the operation is next run without this flag.
	- `find`: Search for the given text in the collections' snippets and list the results.
		- **search** (string, positional, optional): A string to search for. This is a fuzzy search of both title and description.
		- *--tag* (string slice): A slice (list) of tag strings to filter by. Results include all specified tags. Tags are matched exactly. Given tags are made lowercase.
		- *--desc* (string): A string to use for fuzzy searching the description.
		- *--title* (string): A string to use for fuzzy searching the title.
	- `config`, `cfg`: Display subcommands. NOTE: These interact with the app config unless a collection argument is provided.
		- `reset`: Reset the config to the default.
			- *--yes, -y* (bool, default false): A boolean to automatically accept the confirmation message.
			- *--collection, -c* (string): The path to a collection, or a collection alias with the prefix.
		- `open`, `o`: Open the config file in the users preferred editor.
			- *--collection, -c* (string): The path to a collection, or a collection alias with the prefix.
	- `collection`, `col`: Display subcommands.
		- `init`: Initialise a new collection.
			- *--collection, -c* (string): The path to a collection, or a collection alias with the prefix.
		- `remove`, `rm`: Remove the collection (config & metadata).
			- *--yes, -y* (string, default false): A boolean to automatically accept the confirmation message.
			- *--collection, -c* (string): The path to a collection, or a collection alias with the prefix.
		- `reset`: Reset the collection (config & metadata).
			- *--yes, -y* (string, default false): A boolean to automatically accept the confirmation message.
			- *--collection, -c* (string): The path to a collection, or a collection alias with the prefix.
	- `alias`: Display subcommands.
		- `list`, `ls`: List all aliases.
		- `create`, `c`: Create an alias with the given collection.
			- **alias** (string, positional, required): The alias, without the prefix.
			- **collection** (string, positional, optional): The path to the collection root.
		- `remove`, `rm`: Remove the given collection or alias. If neither is provided, remove the collection in the working directory.
			- **alias** (string, positional, optional): The alias, with the prefix.
			- **collection** (string, positional, optional): The path to the collection root.
			- *--yes, -y* (string, default false): A boolean to automatically accept the confirmation message.
		- `rename`: Rename the given alias to the given new alias.
			- **old** (string, positional, required): The old alias, without the prefix.
			- **new** (string, positional, required): The new alias, without the prefix.
	- `tag`: Display subcommands.
		- `add`, `a`: Add the given tag to the given snippet.
			- **tag** (string, positional, required): The tag to add.
			- **snippet** (string, positional, required): The path to the snippet.
		- `remove`, `rm`: Remove the given tag from the given snippet.
			- **tag** (string, positional, required): The tag to remove.
			- **snippet** (string, positional, required): The path to the snippet.
			- *--yes, -y* (string, default false): A boolean to automatically accept the confirmation message.
# TUI

# Project Phases
- `rm` cmd
- `find` cmds
- `config` cmds
- `alias` cmds
- `tag` cmds
- Add barber-ignore to collection config
- Ability to open & edit description in a temporary text file for a given snippet. This will save the changes to the metadata.
- Syntax highlighting.
- TUI style customisation using app config, and overridden by collection config.
- Easily copy snippet to clipboard.
- Easily make new snippet from clipboard.

## Phase 1 - PoC
- `collection` cmds
- `list` cmd
- Build TUI

## Phase 2 - MVP
- `scan` cmd

## Phase 3 - Core

## Phase 4 - Extras

# Config (TOML)
## App
```toml
# App Configuration

default_collection = "./Documents/my_collection"

# TUI Theme Configuration
[theme]
background_color = "black"
text_color = "white"
# Add more style options as needed

# Collection Aliases (Mapping alias to collection path)
[aliases]
"alias1" = "path/to/collection1"
"alias2" = "path/to/collection2"
# Add more aliases as needed

# Git version control options
[git]
auto_init = true
auto_commit = true
```
## Collection
```toml
# Collection Configuration

# Git version control options
[git]
auto_commit = true
```
# Metadata (YAML)
## Collection
```yaml
# Collection Metadata

# List of snippets in the collection
snippets:
  - path: "snippet1.txt"
    description: "This is the first snippet"
    tags: ["tag1", "tag2"]
  - path: "subfolder/snippet2.txt"
    description: "Another snippet in a subfolder"
    tags: ["tag2", "tag3"]
  # Add more snippets as needed
```
# Notes
- The nap interface:
	- ![[CleanShot 2023-10-05 at 08.45.03@2x.png]]
- Previously (in nap) pressing enter on a folder would just focus the snippet list for that folder. In order to create nested folder structure for snippet storage, pressing enter on a folder will change that folder to be opened. This will:
	- show the folders' direct (and optionally inherited) snippets.
	- change the folder list title (default: "Folders") to be "Folders \<folder-name\>"
	- change the folder list to contain any child folders.
	A symbol should be used to indicate whether a folder contains child folders. A symbol should be used to indicate whether a folder is a child folder, or the collection root. Pressing enter on a folder containing no child folders should focus on the snippets list.