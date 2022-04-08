# Format

Every file in this repository describes a product.

## File Name

Each file name embeds a **Product Name** & an optional **Version Number**.

* Without the version number portion, the whole file name yields the **Product Name**.
* Optional **Version Number** is anything between the last `[` and `]` symbols.
  * If **Version Number** appears in the file name, any spaces between the name and `[...]` part will be trimmed from the **Product Name**.

Folder hierarchy currently has no meaning and may be simply used for organization.

* Repetition of the same file (somewhere within the hierarchy):
  * **Undefined Behavior**. The first file found matching desired product name & version number will be accepted as the right source file.

## Content

Any line beginning with a `|` character is a comment line. Make sure to have it as a very first character.

Empty line or several empty lines, including lines containing just white space characters, mean separation between the blocks.

Comment lines do not contribute to block separations.

Every block, line by line, consist of:

* Attachment name or several of attachment names, separated by the `|` character.
  * Each attachment name follows the same rules as the File Name, described above.
* Examples:
  * `Flower`
  * `Flower | Stem`
  * `Flower | Stem | Roots`

* Each of the subsequent lines consist of:
  * `<linkNameAlias>`.
    * `<linkNameAlias>` is a domain-specific alias for a link name.
    * `<linkNameAlias>` is optional. If omitted, it means parent prim (or prim where script resides)
  * `|` Delimiter.
  * `<faceIndex>` - an integer between `0` and `7`.
  * `|` Delimiter.
  * Diffuse Texture. It can be a name if it is readable and located inside the same link as the main script.
  * `|` Delimiter.
  * Normal Texture. Same rule as for Diffuse Texture.
  * `|` Delimiter.
  * Specular Texture. Same rule as for Diffuse Texture.

* Repetition of the same definition:
  * The last one in the file wins.
