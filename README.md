# Format

## File Names

File name represents a Product Name & an optional Version Number.

* Product names cannot contain `|` symbol.
* Optional version number is anything between the last `[` and `]` symbols.
  * If version number is found, any space separators will be removed from the product name.

Folder hierarchy currently has no meaning and may be simply used for organization.

* Repeatition of the same file (somewhere within the hierarchy):
  * Undefined. Basically, the first file found matching desired product name & version number wins.

## Content

Any line beginning with a `|` character is a comment line.

Empty line or several empty lines mean separation between the blocks. Comment lines do not contribute to block separations.

Every block consist of:

* Attachment name or several of them separated by the `|` character.
  * Attachment names follow the same rules as the File Names above.
* Each of the subsequent lines consist of:
  * `<linkNameAlias>` `<optionalDelimiter>` `<faceIndex>` - without spaces in between the parts.
    * `<linkNameAlias>` is a domain-specific alias for a link name.
    * `<optionalDelimiter>` can be any of the following characters: `-`, `/`, `_`, `#`, `\`. Again, space is not allowed.
    * `<faceIndex>` is an integer between `0` and `7`.
  * Delimiter - at least one ` ` (space).
  * Texture UUID. It can be a name if it is readable and located inside the same link as the main script.

* Repeatition of the same definition:
  * The last one in the file wins.
