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
* **Diffuse** zone may contain any of the following types of values:
  *  A UUID.
  *  A name of a texture at:
     *  Prim content, placed along with the in-world script.
     *  Texture Helper HUD (worn or rezzed), where the name of the texture will be matched to a slot name, and return currently selected texture for the slot.
         * The following postifxes after the name on the line (before the **Alpha Mode** changer) - `[d]`, `[n]` or `[s]` - have a special meaning. They get removed to match with the slot names in the Texture Helper HUD, and only used to decide, which of the 3 sub-layers to use, as one mesh face may receive Diffuse, Normal and Specular textures in the Edit Window. If no postifx is provided, it will be assumed that, depending on where the name is used on the line: `[d]` will be used for **Diffuse**, `[n]` for **Normal** and `[s]` for **Specular** section of this line. This default behavior allows to specify the same name on all 3 zones, without bothering with the suffixes. At the same time it gives a possibility of using a texture from another sub-layer for different purpose.
             * Examples:
                 * ` link_name | 0 | GLINT | GLINT | GLINT ` - find a slot with name `GLINT`, take Diffuse layer texture of currently chosen texure for the slot, apply to Diffuse layer on the `link_name`, face `0`, repeat for Normal and Specular by correspondingly applying Normal and Specular sub-layers.
                 * ` link_name | 0 | GLINT | GLINT | GLINT[d] ` - in this case for specular on `link_name`, face `0`, the value will be taken from the slot `GLINT`'s diffuse layer instead of default specular layer.
                 * ` link_name | 0 | GLINT ` - apply only Diffuse map from the Diffuse sub-layer of `GLINT` slot, remove Normal and Specular values.
                 * ` link_name | 0 | GLINT | GLINT_N[d] ` - take for Normal layer a texture from a separately named slot (`GLINT_N`) from its Diffuse sub-layer.
         * Several rezzed or attached Texture Helper HUDs may return their results in unpredictable order, if they have same slot names.
  * The following special names (upper case only) get transformed into their corresponding internal UUIDs and **Alpha Mode** None (`/n` suffix), unless another suffix is specifically mentioned:
      * `BAKED_HEAD`, `BAKED_UPPER`, `BAKED_LOWER`, `BAKED_EYES`, `BAKED_SKIRT`, `BAKED_HAIR`, `BAKED_LEFTARM`, `BAKED_LEFTLEG`, `BAKED_AUX1`, `BAKED_AUX2`, `BAKED_AUX3`.
          * These will be interpreted as special names in the **Diffuse** zones only.
  * Optional suffix starts with the last singular `/`, followed by a list of hints. If any of them don't follow the rules, the `/` suffix falls back to be part of the texture name. Spaces are not required, but can be added for clarity. Example: `/m100 c100,100,100 a50`. Hints described:
    * **Alpha Mode** changers (if several mentioned, the last wins):
      * `n` - **None** (alpha channel is ignored).
          * If the suffix is missing, `/n` is auto-applied to all special `BAKED_` names listed above.
      * `b` - **Alpha Blending**.
          * If the suffix is missing, `/b` is auto-applied to all non-empty texture values.
      * `m255` - **Alpha Masking** with a number between `0` and `255`.
          * If the suffix is missing, `/m255` is auto-applied to all empty texture values.
      * `e` - **Emissive**.
    * **Color**
      * `c255,255,255` - color changer in RGB format (each value within 0..255 range).
        * If the color is missing, it defaults to `255,255,255`.
      * When interacting with the Texture HUD, when both **Color** and **Alpha** hints are missing, those values will be derived from the Texture HUD (provided the name is found among the slots).
    * **Alpha**
      * `a100` - alpha changer (value should be within 0..100 range).
        * If missing, will default to `100` (opaque).
        * `t100` - transparency changer - same as alpha changer, simply works the opposite way (`a0` equals `t100`).
        
* **Normal** zone follows the same rule as for Diffuse Texture (except the `/` suffix portion and special `BAKED_` values).
  * Empty value will be treated as a command to **remove normal texture**.
  * The absense of a named texture among the content and Texture HUD slots currently means skip changing it.
* **Specular** zone follows the same rule as for Diffuse Texture (except special `BAKED_` values as well as `/` suffix, which are different in **Specular**).
  * Empty value will be treated as a command to **remove specular texture**.
  * The absense of a named texture among the content and Texture HUD slots currently means skip changing it.
  * Optional suffix starts with the last singular `/`, followed by a list of hints. If any of them don't follow the rules, the `/` suffix falls back to be part of the texture name. Spaces are not required, but can be added for clarity. Example: `/g100 e5 c255,255,255`. Hints described:
    * **Glossiness**
      * `g255` - glossiness changer (value should be within 0..255 range).
        * If missing, will default to `51`.
    * **Environment**
      * `e255` - environment changer (value should be within 0..255 range).
        * If missing, will default to `0`.
    * **Color**
      * `c255,255,255` - color changer in RGB format (each value within 0..255 range).
        * If the color is missing, it defaults to `255,255,255`.
      * When interacting with the Texture HUD, when both **Color** and **Alpha** hints are missing, those values will be derived from the Texture HUD (provided the name is found among the slots).

> (Any additional data after any subsequent `|` delimiters will be ignored.)

##### Empty Values

Empty value is treated as a command to apply:
* **Diffuse** zone: **Transparent Texture** with **Alpha Mode** set to Masking 255 (`/m255` suffix).
* **Normal** zone: Set to None.
* **Specular** zone: Set to None.

##### Absent Textures

* The absense of a named texture among the content and Texture HUD slots currently means skip changing it. For instance, if a Texture Helper HUD is absent, textures named according to the HUD's slots won't affect corresponding slots. **This may change in the future.**

##### Examples

* `* | 0 | 25d24fe7-805a-4cfb-8cf7-12cfc8ca8ff9` - parent prim, face #0, diffuse map as provided UUID, other maps will be set to none.
* `* | 0 | 25d24fe7-805a-4cfb-8cf7-12cfc8ca8ff9 /m100` - parent prim, face #0, diffuse map as provided UUID with alpha mode set to masking 100, other maps will be set to none.
* `* | 0 | HEAD /m255 c200,200,200` - parent prim, face #0, diffuse map taken from either HEAD texture in the prim content, or Texture HUD, with alpha mode set to masking 255, color 200,200,200, other maps will be set to none.
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

