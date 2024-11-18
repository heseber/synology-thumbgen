# Thumbnail Generator for Synology PhotoStation

Creating thumbnails on the consumer level DiskStation NAS boxes by Synology is incredibly slow which makes indexing of large photo collections take forever to complete. It's much, much faster (days -> hours) to generate the thumbnails on your desktop computer over e.g. SMB using this small Python script. Alternatively, create the thumbnails locally on your desktop or laptop computer before before transferring images and thumbnails to the Synology DiskStation, for example with `rsync`. Thanks to user **tsohr**, creating thumbnails for videos is now supported as well.

## Usage

`python psthumbgen.py --directory <path>`

Example: `python psthumbgen.py --directory photos`

Subdirectories will always be processed. The example above assumes that you are in a directory containing the `photos` subdirectory with your images. You can, of course, also specify an absolute path name.

## Command line options:

`--directory <path>` or `-d <path>` - the directory with your image library

`-eaDir` or `-e` - write directly to the `@eaDir` directory instead of `eaDir_tmp`, works on MacOS and Linux, but not on Windows

`--force` or `-f` - force regeneration of all thumbnails even if they already exist


## Requirements

The script needs the Pillow imaing library (https://pypi.python.org/pypi/Pillow) to be installed:

```sh
pip install Pillow
```

`ffmpeg` is required for creating thumbnails for videos. See https://www.ffmpeg.org/download.html for installation instructions. On MacOS, you can also use homebrew (`brew install ffmpeg`).

## Good to know

Given a file and folder structure like below:

```
photos\001.jpg
photos\dir1\002.jpg
```

...the utility will create the following:

```
photos\eaDir_tmp\001.jpg\SYNOPHOTO_THUMB_XL.jpg
photos\eaDir_tmp\001.jpg\SYNOPHOTO_THUMB_B.jpg
photos\eaDir_tmp\001.jpg\SYNOPHOTO_THUMB_M.jpg
photos\eaDir_tmp\001.jpg\SYNOPHOTO_THUMB_SM.jpg
photos\eaDir_tmp\001.jpg\SYNOPHOTO_THUMB_S.jpg
photos\dir1\eaDir_tmp\002.jpg\SYNOPHOTO_THUMB_XL.jpg
photos\dir1\eaDir_tmp\002.jpg\SYNOPHOTO_THUMB_B.jpg
photos\dir1\eaDir_tmp\002.jpg\SYNOPHOTO_THUMB_M.jpg
photos\dir1\eaDir_tmp\002.jpg\SYNOPHOTO_THUMB_SM.jpg
photos\dir1\eaDir_tmp\002.jpg\SYNOPHOTO_THUMB_S.jpg
```

**On Windows systems:** `eaDir_tmp` is used as a temporary directory name as @ characters are not valid in file names on Windows. Therefore these folders must be renamed to `@eaDir` for PhotoStation to recognize them. This renaming process must be done via SSH to the DiskStation unless the volume is mounted by NFS.

**On MacOS or Linux systems:** You can write directly to `@eaDir`, use the `-e` or `--eaDir` command line option for this.


Useful commands:

```
# remove any existing thumbnail directories, dry-run check print out before running next command!
find /volume1/photos -type d -name '@eaDir' -exec echo '{}' \;

# remove any existing thumbnail directories
find /volume1/photos -type d -name '@eaDir' -exec rm -rf '{}' \;

# rename directories
find /volume1/photos -type d -name 'eaDir_tmp' -exec mv '{}' @eaDir \;
```
