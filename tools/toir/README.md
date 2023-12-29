# Tales of Innocence R Text Translation Tools

Work in progress.

## The scripts

### `extract.py`

This script extracts the text from the game.
It is used as follows:
```
python extract.py <source> <destination>
```
It is assumed that `<destination>` is a directory.
It is assumed that `<source>` is also a directory and has the following structure:
```
<source>/
├─ eboot.bin
└─ toirelease_data/
   └─ _Data 
      └─ ...
```
`eboot.bin` is expected to be the decrypted `eboot.bin` from the game, with the Sony header intact.
The sub-directory `toirelease_data` is expected to contain the decrypted and unpacked `toirelease_data.l7c`.

So far, `extract.py` extracts the following parts:
- `System/ItemDataPack.dat` (items)
- `System/ArtsDataPack.dat` (Artes)
- `System/BattleBookDataPack.dat` (battle book)
- `System/CharaAbility.dat` (character abilities)
- `Field/PackFieldData.dat` (character names)
- `Battle/MissionData.dat` (battle missions)
- `Battle/TutorialData.dat` (battle tutorials)
- `eboot.bin` (system & some menu text)
- `Script/**/*.dat` (story text; some files throw an error---it remains to be checked whether this is critical or not)
- `Field/MapData/*.dat` (NPC and additional story text)
- `Skit/**/*.dat` (skit text)

### `recompile.py`

This script recompiles the executable and data files with the translated text.
It is used as follows:
```
python recompile.py <source> <text> <destination>
```
`<source>` is the same directory as used in `extract.py` and is considered the template for recompilation.
`<text>` is assumed to be a directory containing the various `.csv` files with the translated text (basically the `<destination>` directory from `extract.py`, but with the translations added to the `.csv` files).
Finally, `<destination>` is assumed to be a directory. Its contents is generated by `recompile.py` and used for later reinsertion into `toirelease_data.l7c`.

So far, `recompile.py` recompiles the following parts:
- `ItemDataPack.dat` (items)
- `ArtsDataPack.dat` (Artes)
- `eboot.bin` (system & some menu text)

## Format of extracted `.csv` files

Most of the extracted `.csv` files are self-explanatory, but here is some additional information.

### `ArtsDataPack.dat`

The first column contains the character the Arte belongs to (for example, 0 is Ruca).

### `Skit.csv`

This file is probably the most complex.
It has five columns (excluding the translation column):
- *File*, which is simply the name of the skit file
- *Field*, which is one of `speaker`, `line_speaker`, and `line`
- *Index*
- *Speakers*, which is only non-empty for `line`
- *Japanese*, which contains the text

Add the beginning of each file, a list of main speakers is given, indicated by the `speaker` in the *Field* column.
This needs to be translated.
The actual text lines of the skit follow. For each row where *Field* is `line`, the column *Speakers* lists the characters from the main speakers participating in that line so translators know which characters are speaking to match their tone.
For convenience, the actual character name is listed together with the character index, **but this does not need to be translated**.
It is taken automatically from the main speaker list.
However, some lines are preceded by a row where *Field* is `line_speaker`.
Each of these rows specifies the speaker of the following line directly and needs to be translated.

**In summary: Just translate the Japanese text of each row in the column *Japanese***.
The *Speakers* column aids in matching the speaking tone.

### A note about `Script.csv` and `Skit.csv`

Both files may contain the control codes `{fixed}` and `{variable}`, which switch between fixed and variable spacing.
In most cases, these can be ignored and they are only relevant if some text is required to be rendered with fixed spacing.

Not that the original script switches between these two spacings constantly, using variable spacing for punctuation and fixed for Japanese characters.
This is filtered out as best as possible to not clutter the script.
However, in some odd cases remnants of this can be seen.