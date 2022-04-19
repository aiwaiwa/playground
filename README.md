# Format

Every file in this repository describes a product. The content consists of rules to apply to the faces of the product prims, whether a parent (where the in-world script lives) or a child prim, based on availability of non-HUD attachments.

## File Name

Each file name embeds a **Product Name** and an optional **Version Number**. `README.md` file is skipped from search.

* Without the version number portion, the whole file name yields the **Product Name**.
* Optional **Version Number** is anything between the last `[` and `]` symbols.
  * If a **Version Number** appears in the file name, any spaces between the name and `[...]` part will be trimmed from the **Product Name** part.

An in-world prim containing the script will use its Object name in attempt to retrieve the data that is meant for it. A prim name will be split into a **Product Name** and optional **Version Number** the same way as the File Name in this repository.

Folder hierarchy currently has no meaning and may be simply used for organization.

### Repetition of the same file (somewhere within the hierarchy)

Currently it is **undefined** which file to pick if there is several valid matches found, any one of them may become a winner.

## File Content

### Comments

Comment separator is a double forward slashes (`//`). Any line beginning with two forward slashes (`//`) is a full comment line and will be ignored by the file parser. Full comment lines can have white space character(s) in front of the `//`. Full comment lines do not contribute to block separations, described later, so they can be freely used anywhere within the file without affecting how the blocks start and end.

* A comment can also appear anywhere within the lines. Everything after `//` will be ignored. This may be used to temporarily disable the rest of the line, or to provide a comment.

### Blocks

A single empty line or several empty lines, including lines containing only white space characters, serve as a separation between the blocks.

Every block, line by line, consist of:

#### First Line - Attachment Rule

* Attachment name or several attachment names. Behavior described below first line will apply to all attachments listed here, separated by `|` character.
  * Each attachment name follows the same rules as the File Name, described above.
  * Spaces around `|` may be added for readability.
  * The product name (matching the file name) itself can be a sole rule requirement, which would basically define a default set of textures for the worn product.
    * An alias for the product name is a one asterisk `*` symbol. This is helpful as it works out-of-box when the file name changes, which otherwise would require renaming every instance of the product name in the rule lines.
    * This rule will have no affect on the product if it is simply instantiated on the ground.

##### Examples

* `*` - the product itself (for which the file name is provided) is worn.
* `Flower` - a prim named `Flowr` is worn.
* `* | Flower` - the product itself is worn, plus a prim named `Flower`.
* `Flower | Stem` - both `Flower` and `Stem` are worn.
* `Flower | Stem | Roots` - all three attachments are worn.
* `Flower [1.0] | Stem` - a `Flower [1.0]` and a `Stem` are worn.
  * Whether a prim name contains zero or more than one spaces between `Flower` and `[1.0]`, or the rule does, they will still match each other.

#### Each of the Subsequent Lines

Each line after the first in the block describes a texture data until the block separator or the end of file. 

Each line consists of `|`-separated fields (spaces around `|` may be added for readability):

* `<linkNameAlias>` - a domain-specific alias for a link name. Currently simply matches the link name (first one found in the link-set with this name wins).
  * Parent prim alias - where the in-world script resides - an asterisk symbol (`*`).
* `|` character as delimiter.
* `<faceIndex>` - an integer between `0` and `7`.
* `|` character as delimiter.
* **Diffuse Texture**. Either an UUID or a name of a texture inventory item placed along with the in-world script. The name can contain spaces.
  * Non-mentioned texture will be treated as a command to apply **transparent texture**.
  * The suffix starting with the last singular `/` is treated as an **Alpha Mode** changer only if it matches one of the following:
    * `/n` - **None** (alpha channel is ignored).
    * `/b` - **Alpha Blending**.
    * `/m200` - **Alpha Masking** with a number between `0` and `255`.
      * Add spaces to your liking: `/ m 200`, `/ m200`, etc.
    * `/e` - **Emissive**.
* `|` character as delimiter.
* **Normal Texture** - follows the same rule as for Diffuse Texture (except the `/` suffix portion).
  * Non-mentioned texture will be treated as a command to **remove normal texture**. 
* `|` character as delimiter.
* **Specular Texture** - follows the same rule as for Diffuse Texture (except the `/` suffix portion).
  * Non-mentioned texture will be treated as a command to **remove specular texture**.
* (Any additional data after any subsequent `|` delimiters will be ignored.)

##### Examples

* `* | 0 | 25d24fe7-805a-4cfb-8cf7-12cfc8ca8ff9` - parent prim, face #0, diffuse map as provided UUID, other maps will be set to none.
* `* | 0 | 25d24fe7-805a-4cfb-8cf7-12cfc8ca8ff9 /m100` - parent prim, face #0, diffuse map as provided UUID with alpha mode set to masking 100, other maps will be set to none.
* `* | 0 | 25d24fe7-805a-4cfb-8cf7-12cfc8ca8ff9 | normal_map_001` - parent prim, face #0, diffuse map as provided UUID, normal map from inventory "normal_map_001", specular map will be set to none.
* `Hair | 1 | fluffy | | fluffy_sp` - prim `Hair`, face #1, diffuse map `fluffy`, normal map set to none, specular map will be taken from the `fluffy_sp` inventory.
* `Hair | 2` - prim `Hair`, face #1, apply transparent diffuse texture, and remove both normal and specular ones.

### Repetition of the same definition

The last matching rule in the product file wins.

## How it works

In its basic form, a file may describe default textures when corresponding product is the only one attached. Simply put the product name as the first-line rule (or use an asterisk `*` alias), and then mention the textures affected. This may include applying transparency by default.

> As this rule implies the product being attached, simply placing it on the ground will set to transparent any mentioned prims and faces. To override this, a copy of the same product can be temporarily attached.

Only the prims and faces mentioned somewhere in the file will be modified.

Any other face texture settings like texture rotation or texture repeats will not be changed.

