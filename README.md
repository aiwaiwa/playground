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

* A comment can also appear anywhere after meaningful characters. Everything after `//` will be ignored. This may be used to temporarily disable the rest of the line, or to provide a comment.

### Blocks

A single empty line or several empty lines, including lines containing only white space characters, serve as a separation between the blocks.

Every block, line by line, consist of:

#### First Line - Attachment Rule

Attachment name or several attachment names. Behavior described below first line will apply to all attachments listed on this line, separated by `|` character, when all of them are found attached to the body. In other words, this line set (L), in relation to all currently worn attachments (A) will be only used in calculations, when L is a [subset](https://en.wikipedia.org/wiki/Subset) of A (L⊆A). HUDs cannot be counted as attachments.

* Each attachment name follows the same rules as the File Name, described above.
* Spaces around `|` may be added for readability.
* The product name (matching the file name) itself can be a sole rule requirement, which would basically define a default set of textures for the worn product.
    * An alias for the product name is an asterisk `*` symbol. This is helpful as it works out-of-box when the file name changes, which otherwise would require renaming every instance of the product name in the rule lines.
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

```
LinkName | FaceIndex | Diffuse | Normal | Specular 
```

* **LinkName** - Matches the link name (first one found in the link-set with this name wins).
  * There is a parent prim alias: Use either spaces, or an asterisk symbol (`*`).
* **FaceIndex** - an integer between `0` and `7`.
* **Diffuse**. Either:
  *  A UUID.
  *  A name of a texture at:
     *  Prim content, placed along with the in-world script.
     *  Texture Helper HUD (worn or rezzed), where the name of the texture will be matched to a slot name, and return currently selected texture for the slot.
         * The following postifxes after the name on the line (before the **Alpha Mode** changer) - `[d]`, `[n]` or `[s]` - have a special meaning. They get removed to match with the slot names in the Texture Helper HUD, and only used to decide, which of the 3 sub-layers to use, as one mesh face may receive Diffuse, Normal and Specular textures in the Edit Window. If no postifx is provided, it will be assumed that, depending on where the name is used on the line: `[d]` will be used for **Diffuse**, `[n]` for **Normal** and `[s]` for **Specular** section of this line. This default behavior allows to specify the same name on all 3 columns, without bothering with the suffixes. At the same time it gives a possibility of using a texture from another sub-layer for different purpose. Examples:
             * ` link_name | 0 | GLINT | GLINT | GLINT ` - find a slot with name `GLINT`, take Diffuse layer texture of currently chosen texure for the slot, apply to Diffuse layer on the `link_name`, face `0`, repeat for Normal and Specular by correspondingly applying Normal and Specular sub-layers.
             * ` link_name | 0 | GLINT | GLINT | GLINT[d] ` - in this case for specular on `link_name`, face `0`, the value will be taken from the slot `GLINT`'s diffuse layer instead of default specular layer.
             * ` link_name | 0 | GLINT ` - this means avoid applying Normal or Specular values.
             * ` link_name | 0 | GLINT | GLINT_N[d] ` - this means to take for Normal a texture for a separately named slot (`GLINT_N`) and from its Diffuse sub-layer.
         * Several rezzed or attached Texture Helper HUDs may return their results in unpredictable order, if they have same slot names.
  * The following special names (upper case only) get transformed into their corresponding internal UUIDs and **Alpha Mode** None (`/n` suffix), unless another suffix is specifically mentioned:
      * `BAKED_HEAD`, `BAKED_UPPER`, `BAKED_LOWER`, `BAKED_EYES`, `BAKED_SKIRT`, `BAKED_HAIR`, `BAKED_LEFTARM`, `BAKED_LEFTLEG`, `BAKED_AUX1`, `BAKED_AUX2`, `BAKED_AUX3`.
          * These will be interpreted as special names in the **Diffuse** zones only.
  * Empty value is treated as a **transparent texture** plus **Alpha Mode** set to Masking 255 (`/m255` suffix).
  * After trying to locate name inside 
  * The following special local textures will be transformed into current UUIDs, taken from a currently attached Local Texture Helper:
  * The absense of a local texture will be translated into a transparent texture on **Diffuse** zone, and None texture for both **Normal** and **Specular** zones.
  * Optional suffix starting with a singular `/` is treated as an **Alpha Mode** changer. If it doesn't match any of the following patterns, it is considered simply a remaing part of the texture name:
    * `/n` - **None** (alpha channel is ignored).
        * If the suffix is missing, `/n` is auto-applied to all special `BAKED_` names listed above.
    * `/b` - **Alpha Blending**.
        * If the suffix is missing, `/b` is auto-applied to all non-empty texture values.
    * `/m200` - **Alpha Masking** with a number between `0` and `255`.
        * If the suffix is missing, `/m255` is auto-applied to all empty texture values.
        * Spaces between `/`, `m` and the number are allowed and will be ignored.
    * `/e` - **Emissive**.
* **Normal** - follows the same rule as for Diffuse Texture (except the `/` suffix portion and special `BAKED_` values).
  * Empty value will be treated as a command to **remove normal texture**. 
* **Specular** - follows the same rule as for Diffuse Texture (except the `/` suffix portion and special `BAKED_` values).
  * Empty value texture will be treated as a command to **remove specular texture**.

> (Any additional data after any subsequent `|` delimiters will be ignored.)

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

