# Format

## File Names

File name represents a Product name & optional Version number.

* Product names cannot contain `|` symbol.
* Optional version number is anything between the last `[` and `]` symbols.
  * If version number is found, any space separators will be removed from the product name.

Folder hierarchy currently has no meaning and may be simply used for organization.

## Content

Empty line or several empty lines mean separation between the blocks.

Blocks consist of:
* Attachment name or several of them separated by the `|` character.
  * Attachment names follow the same rules as the File Names above.
* Each of the subsequent lines consist of:
  * `<linkNameAlias>` `<optionalDelimiter>` `<faceIndex>` - without spaces in between the parts.
    * `<linkNameAlias>` is a domain-specific alias for a link name.
    * `<optionalDelimiter>` can be any of the following characters: `-`, `/`, `_`, `#`, `\`.
    * `<faceIndex>` is an integer between `0` and `7`.
  * Delimiter - at least one ` ` (space).
  * Texture UUID. It can be a name if it is readable and located inside the same link as the main script.

### Example

```
Apple
B2 88ae580d-b3fb-ccb5-6a0e-6eaf19deb934
B3 27145bc7-1ccf-cb4c-2d1b-5e5c9b35cd77
A0 c6c1ee60-72b5-4cf9-a75f-aaad4192ddf9
```
