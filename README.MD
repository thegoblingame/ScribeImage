## ABOUT

ScribeImage is a bash script that takes a batch of JPGs/PNGs, scans them for text with Tesseract OCR, and adds any detected text to the "Title" metadata tag.

My motivation for making this is that I frequently have trouble finding specific memes in my collection. This is intended to make it easier, because you can search for JPGs based on the text contained in their metadata in the Windows 10 file explorer. It should also work on Windows 11 although I haven't tested that as much.

Please feel free to suggest improvements or mention any bugs you notice.

## SCRIPT USAGE

Thus far, I've designed this script to work with Windows 10. The script itself can be run in any bash terminal, but I don't know how the searching would work on other operating systems. I may add support for other OS's later.

1. Clone the repo
2. Install tesseract, imagemagick, and exiftool with whatever package manager you want. Here's how you would do it with chocolatey. `choco install tesseract imagemagick exiftool -y`

3. Delete placeholder images in input/output_images folder (git doesn't track empty directories)

4. Copy all JPGs/PNGs that you want to be tagged to the input_images folder. I'd recommend keeping backup copies just in case something weird happens. I squashed bugs that showed up on my end but who knows what kind of weird JPGs/PNGs people have. Filenames should be UTF-8.

5. Run the script with whatever flags you want. Example: `./image_scribe.sh -q -d`

6. After the script finishes running, you can find your modified JPGs/PNGs in the output_images folder, and logging in the logs.txt file.

7. (optional) I'm not 100% sure why but due to the way files are being added to the output_images folder, the search indexing on the folder will be messed up, that is to say, some images containing "X" won't necessarily show up for a few days. There are 3 ways to fix this:

- manually move the processed images in output_images to another folder. I recommend this option
- wait for a few days
- Control Panel => search for Indexing Options => Advanced => Rebuild

Keep in mind the script does not clear either the input or output folders before or after it finishes running. So if you want to do that you need to do it manually.

### FLAGS

When I was testing this I created some scripts to clean up my images folder. If you add some flags to the bash command it will run that code before it runs the main one that actually processes the images with tesseract. There are also some flags concerning logging.

-q: This flag doesn't run a script, but removes all logging from the terminal and any logging in logs.txt that don't come from exiftool. You may want to do this if you just want the script to run as fast as possible.

-d: This flag runs code that detects duplicate file names and appends _n to the end of the filename. This is necessary when you have JPGs/PNGs with the same file name because all pngs get converted to jpegs in order to be searchable.

-j: This flag runs code that detects files with the .PNG extension that have JPEG as their MIME type (so basically fake PNGs) and changes their extension to JPG. I had a bunch of these for some reason, IIRC they were mobile screenshots.

## SEARCHING

Open up an instance of file explorer.

If you haven't already, click the "View" option in the top left => click "Details pane". This pops up a little section on the right-hand side that will give you extra details about any file you click on in the explorer. For images, the pane will display (some) of the metadata tags. Any JPGs/PNGs that have been run through ScribeImage should have some values in the fields.

To search for an JPG based on its metadata, use the search bar in the top right with the following syntax: ${tag}: ${value}. The search is case insensitive, and will match partial strings.

So for example, title: cat could return images with the following values in their title tag: "cat", "CAT", "cute kittycat", "he will catch the ball".

## Q & A

### Why not just use Google Photos to search for images?

AFAICT, Google Photos does a great job visually identifying objects in photos, but it's inconsistent when it comes to detecting text. For example, when I search through my images on google photos for the word "Anon" (to pull up all 4chan screencaps), it shows about 200 results, but searching as described in the above section yields about 500 results. Google Photos also doesn't display any metadata on an image except for the location.

### What image formats can I use?

JPG/JPEG/PNG. Any PNGs will be converted to JPGs before they are scanned by Tesseract.

### Why does the script convert PNG to JPEG’s?

While EXIFTOOL has the capability of updating metadata tags on PNGs - I don’t know of a way to search for PNGs based on their metadata via the Windows 10 UI, so I convert them to JPEGS. If you happen to know of a way to do so, please let me know.

### I'm searching for a keyword and my image isn't showing up even though I'm positive it has the text inside of it, WTF?

Presumably for compute reasons, if a metadata tag contains text that is more than 1,000 characters, the Windows 10 UI won't be able to search it

### How come this doesn't have [insert feature here]

90% chance I couldn't figure out how to do it and/or didn't feel it was worth the time investment. Please feel free to suggest improvements or mention any bugs you notice! As of May 2024 I'm unemployed and working on personal coding projects for fun right now so I have the time.

## TODO

- see if there is a better alternative to tesseract
- add functionality for other image types?
- decide how to handle instance where script processes an image that is already in output_images
- get working on other OS
- mobile version?
- flag that allows user to cut off text at 999 characters so images with a lot of text are still searchable
- flag that clears output/input folders before/after script runs
- look into filename character issue again