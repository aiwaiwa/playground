# Format

Every file in this repository describes a product. The content consists of rules to apply to the faces of the product parent (where the in-world script lives) and child prims.

## File Name

Each file name embeds a **Product Name** & an optional **Version Number**.

* Without the version number portion, the whole file name yields the **Product Name**.
* Optional **Version Number** is anything between the last `[` and `]` symbols.
  * If a **Version Number** appears in the file name, any spaces between the name and `[...]` part will be trimmed from the **Product Name** part.

An in-world prim containing the script will use its Object name in attempt to retrieve the data that is meant for it. A prim name will be split into a Product Name & optional Version Number the same way as the File Name in this repository.

Folder hierarchy currently has no meaning and may be simply used for organization.

### Repetition of the same file (somewhere within the hierarchy)

Currently it is **Undefined** which file to pick if there is a conflict.
The first file found matching desired product name & version number will be accepted as the right source file.

## File Content

Any line beginning with a `|` character is a comment line. It has to be the _very first character_ of a line.

Empty line or several empty lines, including lines containing only white space characters, serve as a separation between the blocks.

Comment lines do not contribute to block separations, so they can freely appear on any line.

Every block, line by line, consist of:

* First Line
  * Attachment name or several attachment names. Behavior described below will apply to all attachments listed here, separated by `|` character.
    * Each attachment name follows the same rules as the File Name, described above.
  * Examples:
    * `Flower` - a prim named `Flower` is worn.
    * `Flower | Stem` - both `Flower` and `Stem` are worn.
    * `Flower | Stem | Roots` - all three attachments are worn.
    * `Flower [1.0] | Stem` - a `Flower [1.0]` and a `Stem` are worn. Whether a prim name contains zero or more than one spaces between `Flower` and `[1.0]`, or the rule does, they will still match each other.

* Each of the subsequent lines consist of:
  * `<linkNameAlias>` - a domain-specific alias for a link name. Currently simply matches the link name (first one found in the linkset with this name wins).
    * Parent prim (where the in-world script resides) can be substituted with a space or several spaces.
      * At least one space has to be provided, otherwise the line will be considered a comment.
  * `|` character as delimiter.
  * `<faceIndex>` - an integer between `0` and `7`.
  * `|` character as delimiter.
  * Diffuse Texture. Either an UUID or a name if it is readable and located inside the same link as the main script.
    * Absense of this texture means to apply **transparent texture**.
  * `|` character as delimiter.
  * Normal Texture. Same rule as for Diffuse Texture.
    * Absense of this texture means **removing normal texture**. 
  * `|` character as delimiter.
  * Specular Texture. Same rule as for Diffuse Texture.
    * Absense of this texture means **removing specular texture**.
  * (Any additional values after any subsequent `|` delimiters will be trimmed.)
  * Examples (phrase `<space>` stands for a space character ` `, otherwise not seen):
    * `<space> | 0 | 25d24fe7-805a-4cfb-8cf7-12cfc8ca8ff9` - parent prim, face #0, diffuse map UUID.
    * `<space> | 0 | 25d24fe7-805a-4cfb-8cf7-12cfc8ca8ff9 | normal_map_001` - parent prim, #0 face, diffuse map UUID, normal map from inventory "normal_map_001".
    * `Hair | 1 | fluffy | | fluffy_sp` - prim `Hair`, face #0, diffuse map `fluffy`, no normal map, specular map `fluffy_sp`.

Spaces surrounding `|` characters will be removed from both ends, which allows for spaces anywhere inside the link names or texture names. This also help in decorating the lines, ex. aligning the columns of data.


### Repetition of the same definition

The last one in the file wins.
