# Amethyst Metadata File (.amf)

<img align="right" src="https://github.com/Geoxor/amethyst/raw/master/assets/images/amf.png" alt=".amf" width="128"/>

This file is stored in the `Metadata Cache` folder within `%appdata%/amethyst` and it's a [JSON](https://en.wikipedia.org/wiki/JSON) file containing
the metadata of a given audio file, the filename is gonna be the same same as the name of the audio file it stores metadata for

eg. `enjoii - Never Say Goodbye.flac` => `enjoii - Never Say Goodbye.flac.amf`


## Regeneration
These files are generated when Amethyst analyzes a file for metadata, they can be rewritten by forcefully refreshing metadata
within Amethyst from the dropdown menu or with the shortcut `CTRL + ALT + R`

## Deletion
As mentioned above all metadata caches are stored in `%appdata%/amethyst/Metadata Cache` simply delete all the files within the folder
