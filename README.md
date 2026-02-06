# Pokémon Rhizome

Built from the [Maruno17 Pokemon Essentials Base Project](https://github.com/Maruno17/pokemon-essentials).

## Setup

1. Make sure you have [RPG Maker XP](https://www.rpgmakerweb.com/products/rpg-maker-xp) installed.
2. Clone this repository
3. Once successfully cloned, compile the `.rxdata` files locally by running these commands:
> ```bash
> $ ./unpacked.exe --combine --force
> $ ./unpacked.exe --extract
> ```
4. Use RPGMaker XP to open the project (select the `Game.rxproj` file)
5. Run the game using RPG Maker to run it in debug mode at least once. This will compile the PBS files into `.dat` files to be used in-game. These will also be recompiled whenever changes are detected in the PBS files.
6. Only after step 5 will you be able to successfully run the Game.exe without having to open RPG Maker XP, if you so wish.

## Details: Binaries vs. yamls/scripts

RPGMaker XP by default uses **compressed binary files** to store the entirety of the project data (excluding graphical), in the form of `.rxdata` and `.dat` files. This means that
the files are not human-readable, and cannot be meaningfully edited with a text editor. You are expected to use the RPGMaker editor only. This makes it particularly bad for version control,
because git does not attempt to track any line-by-line diffs for binary files. It would also bloat the size of the repository because of this - since diffs typically allow git to avoid keeping entire copies 
of the files for each iteration. All ruby scripts are stored in a single `Scripts.rxdata` file and it is (typically) expected that you don't edit scripts outside of the RPGMaker XP built-in editor.

Pokemon Essentials builds on top of RPGMaker XP, but bypasses this design. It implements utilities to convert from binary `.rxdata` files into human-readable and git-friendly ruby (`.rb`) script files.
Compatibility with the editor is ensured by replacing the old `Scripts.rxdata` file with a stub file that simply points to and reads from the extracted `.rb` files.

We took this one step further by implementing the same process for the rest of of the `.rxdata` files, and avoiding the use of binaries entirely within our repo.

## Scripts

The scripts no longer live in the Scripts.rxdata file (as they do for typical RPGMaker XP projects). 
They have been extracted into separate files and placed in the Data/Scripts/ folder (and subfolders within). This makes it easier to work with other people and keep track of changes.

The scripts are loaded into the game alphanumerically, starting from the top folder (Data/Scripts/) and going depth-first. That is, all scripts in a given folder are loaded, and then each of its subfolder is checked in turn (again in alphanumerical order) for files/folders to load/check.

### Extracting and reintegrating binaries

To convert back to the binary files, run the following command from the root directory of the project:

> ```bash
> $ ./unpacked.exe --combine
> ```

When running `--combine`, the old text files (`.yaml`/`.rb`) will not be deleted, but they
will no longer be hooked into the project. Instead, the `.rxdata` files will contain ALL the data, the same way a standard RPGMaker XP project operates.

To extract the binary files into text files, run the following command from the root directory of the project:

> ```bash
> $ ./unpacked.exe --extract
> ```

When running `--extract`, the old binary files will be **replaced**, and will instead contain stub loader scripts that read from the text. Thus, even when extracted, the game should still be playable. 
Performance may be worsened with this text-based scheme, however, so you are free to extract and combine as you please - as long as you ensure that the files are fully extracted before commiting. 
***No binary files in the git, please!***

You'll notice that the commands here are the same ones you run when you first clone the repo. Doing a quick combine and extract lets us generate the `.rxdata` loader files automatically.

## Files not in the repo

The .gitignore file lists the files that will not be included in this repo. These are:

* All binary files in the `Data/` folder, except
  * `Scripts.rxdata` (a special version that just loads the individual script files).
* Some files that are generated as backup by the `unpackd.exe` utility
* A few files in the main project folder (two of the Game.xxx files, the RGSS dll file and errorlog.txt).
* Temporary files, IDE files, and DS_Store stuff.
