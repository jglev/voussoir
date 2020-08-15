# voussoir *("voo-swar")*

A single-camera solution for book scanning.

This program takes images of books (each picture including either one page or a two-page spread), detects special glyphs pasted in the corners of the book's pages, and **[de-keystones](https://en.wikipedia.org/wiki/Keystone_effect "Wikipedia: 'Keystone Effect'") and thereby digitally flattens the pages.** It then **automatically separates the pages into separate, cropped image files.**

## Why is this program called "voussoir"?

A [voussoir](https://en.wikipedia.org/wiki/Voussoir "Wikipedia: 'Voussoir'") is a stone in an archway; a [keystone](https://en.wikipedia.org/wiki/Keystone_(architecture) "Wikipedia: 'Keystone'") is a type of voussoir. Voussoir (the software) is meant to be one piece among several in a book scanning workflow (like one of several stones that compose an arch), and deals centrally with the ["keystone effect,"](https://en.wikipedia.org/wiki/Keystone_effect "Wikipedia: 'Keystone Effect'"), which is named after the type of arch stone : )

Other useful open-source programs to use when book scanning include [darktable](http://www.darktable.org/ "darktable") and [ScanTailor](http://scantailor.org/ "ScanTailor"). An example workflow could include voussoir to de-keystone and crop images, darktable to adjust contrast and sharpen images, and ScanTailor to do final processing.

# Example

The program takes images like this:

<a href="Example_Images/test_input.jpg" >
	<img src="Example_Images/test_input.jpg" alt="[Keystoned image example]" height="256px">
</a>

And turns them into images like these:

<a href="Example_Images/test_output_left_page.jpg" style="
	display: inline-block;
	float: left;
	padding-right: 1em;
	">
	<img src="Example_Images/test_output_left_page.jpg" alt="[De-keystoned left page]" height="256px">
</a>

<a href="Example_Images/test_output_right_page.jpg" style="
	display: inline-block;
	float: left;
	">
	<img src="Example_Images/test_output_right_page.jpg" alt="[De-keystoned right page]" height="256px">
</a>

# Background

This program is an annotated and expanded fork of a one-day build written in 2012 by [Yutaka Tsutano](http://yutaka.tsutano.com/projects/others/ "Yutaka Tsutano's website"). Videos of Mr. Tsutano's prototype book scanner build and software tests are available here:

* [Book Scanner: First Prototype](http://www.youtube.com/watch?v=rjzxlA9RWio)
* [Book Scanner: Marker Test](http://www.youtube.com/watch?v=YXANjnry6CU)
* [Book Scanner: Image Processing Test #1](http://www.youtube.com/watch?v=lHHPFBH2EkA)

Mr. Tsutano posted the code after receiving requests from viewers of the above videos, and kindly released the code under the permissive [ISC license](https://en.wikipedia.org/wiki/ISC_license "ISC License") (which is [functionally equivalent to](http://choosealicense.com/licenses/#isc "ChooseALicense.com: ISC License") the [BSD 2-Clause](http://choosealicense.com/licenses/bsd-2-clause/ "BSD Two-Clause License") and [MIT](http://choosealicense.com/licenses/mit/ "MIT License") licenses) in 2016.

Mr. Tsutano noted in the code's original readme that "this program was written in ad-hoc manner without thinking about publishing the source code. It was just a test of concept. So I published it. But it was a project that was done in a day years ago, and I have no time to verify it still works today." Jacob Levernier, a member of the [DIY Book Scanning](http://DIYBookscanner.org "DIYBookScanner.org") community, forked the codebase in 2016 in order to better document and clean it for a wider audience. The program now has a documented command-line interface that supports any size of book and fine-tuned adjustment of output image borders.

## Compilation

### Install OpenCV and `cmake`:

* **MacPorts:** `sudo port install opencv cmake`
* **Linux:**
	* **openSUSE**: `sudo zypper install opencv cmake libopencv2_4 opencv-devel`
	* **Ubuntu**: `sudo apt-get install opencv cmake opencv-dev` (This will also install opencv dependencies).

### Confirm that you have installed (and are using) `gcc` 4.9+

This program uses [docopt](https://github.com/docopt/docopt.cpp "Docopt.cpp GitHub page"). Docopt does not need to be installed separately (i.e., it's bundled within this repository), but it *does* depend on `gcc` 4.9 or greater.

If you are getting an error when compiling that includes "undefined reference to `std::regex_token_iterator`", run `gcc --version` and confirm that your `gcc` version is greater than or equal to 4.9. If not, you need to upgrade `gcc.` Note that `g++ --version`, `c++ --version`, and `cpp --version` should similarly show 4.9 or greater.

If you've upgraded and are still seeing the error, it's likely that your system is still trying to use the lower version. In this case, one way to explicitly tell cmake which version to use (for example, version 5) is to replace the command `cmake ./` below with `cmake -DCMAKE_CXX_COMPILER=g++-5 .`.

*\[If you successfully build the program on another type of system, please let me know, and I'll add the required packages here.]*

### Compile using `cmake` (See above for information regarding telling `cmake` explicitly which version of c++ to use):

```
cd [directory of the source code]
cmake ./
make
```

The binary executable will be saved under `./bin/voussoir`, along with .pdf and .ai copies of the glyphs/markers discussed below.

# Usage

## Getting Ready to Use the Program / Taking Pictures

Within the `markers` directory, you'll find PDF and Adobe Illustrator / Inkscape versions of a series of 15 "glyphs," small images that each comprises a unique pattern of pixels in a 6x6 grid. You'll need to print and cut out the glyphs; currently, only glyphs 0-3 (left page) and 4-7 (right page) are needed.

Tape or otherwise affix the glyphs in **clockwise order** around the perimeter of each book page (for example, if you're using a glass or acrylic [platen](http://diybookscanner.org/ "DIYBookScanner.org -- Scroll down for a description of each part of a book scanner") to flatten the pages of a book, affix the glyphs in each corner of the platen: **starting at the top left and moving clockwise to the center/spine of the book,** glyphs 0, 1, 2, and 3 on the left page, and (again from top left and moving clockwise) glyphs 4, 5, 6, and 7 on the right page.

Alternatively, instead of moving the markers for each book you scan, you can more permanently affix the markers to the outside of your platen or book cradle, and then use the program's "offset" command-line options to adjust the output images for each book.

**Electrical tape** works well to affix the markers to glass or acrylic, because it is unlikely to leave residue.

When you take photos, it should be under **bright, even lighting,** in order to make both the book pages and the glyphs visible and crisp. One of the advantages of using this program is that you can take photos from off center -- as long as the glyphs can all be clearly seen and are not blurry in the image, each page will be de-keystoned as if it had been photographed perpendicularly (i.e., straight on) from the camera. 

One useful workflow with this program is to set up your camera, take several test photos, and confirm that the program will be able to recognize the glyphs in them. Once you have confirmed this, you can scan the full book.

Do note that the program only transforms the pages as two-dimensional objects; that is, it **assumes that the pages are each (individually) flat** (e.g., under a glass or acrylic platen). If the pages are **warped/curled** in addition to being keystoned, this program will not correct the curl (correcting curl is difficult to do; [ScanTailor](http://scantailor.org/ "ScanTailor"), also linked below, makes a good effort by looking for curves in lines of text on each page, as do commercial options like [Booksorber](http://booksorber.com/ "Booksorber"), which tries to find the outline of the book's pages; in general, though, dewarping a page consistently likely requires a more complicated setup using [lasers](https://github.com/duerig/laser-dewarp "Laser Dewarper DIY book scanning software") or [infrared light](https://www.youtube.com/watch?v=03ccxwNssmo "YouTube: 'BFS-Auto: High Speed Book Scanner at over 250 pages/min'").

## Using the Program

The program takes six basic arguments:

`./voussoir --page-height 10 --page-width 6 --input-image test_input.jpg output_left.jpg output_right.jpg`

In this example, `test_input.jpg` is the input file name, and the final two arguments are the
output file names. The page is 10 units high and 6 units wide (these can be inches or any other unit -- what matters is their relation to each other). If you do not specify a height and width, they will default to 9.5 and 6, respectively.

Run `./bin/voussoir --help` to see additional options for making cropping ("offset") adjustments to each edge of each page, and/or for specifying that you only want to process a left page or right page (vs. both pages).

### Debugging using a Webcam

To debug using a webcam, execute the program without an input file argument:

```
./voussoir
```

If a webcam is found, this will cause a window to open, showing output from the webcam. When the four "left page" glyphs (i.e., glyphs 0, 1, 2, and 3) are detected by the webcam, a new window will open showing the de-keystoned image that the four glyphs surround. Similarly, when the four "right page" glyphs (i.e., glyphs 4, 5, 6, and 7) are detected by the webcam, an additional new window will open, showing the de-keystoned image for those four glyphs. Throughout this process, debugging text will be given in the terminal window, including which glyphs are detected.

### Example Scripts

The Example_Images directory in this repository contains two example scripts.

* `example_automated_script_that_watches_a_folder.sh` provides an approach for watching a directory and automatically processing any images that are placed into it.
* `example_command_invocation_for_test_input_jpg.sh` provides an example invocation for the program for the image located in this repository under `Example_Images/test_input.jpg`.

### More on Book Scanning

* If you're interested in building some **hardware** to flatten the pages of the book, I recommend exploring the [DIYBookScanner.org gallery and forums](http://diybookscanner.org/intro.html "DIYBookScanner.org: Gallery"). An especially popular and easy-to-build model was created by a gentleman named David Landin in 2013 out of **PVC plumbing pipe,** and can be viewed [here](http://diybookscanner.org/forum/viewtopic.php?f=14&t=2914 "DIYBookScanner.org: David Landin model").
* Additionally, the founder of the DIYBookScanning community, [Daniel Reetz](http://www.danreetz.com/ "Daniel Reetz"), wrote an extensive [monograph](http://diybookscanner.org/archivist "DIYBookScanner.org: The Archivist") on DIY scanning hardware in 2015, when he retired from the project (see also [Mr. Reetz' blog post](http://www.danreetz.com/blog/2015/12/31/internet-dan-seems-to-be-dead/ "Daniel Reetz: 'Internet Dan Seems to be Dead'") about the site). Mr. Reetz' expertise on the topic is unmatched; his monograph is, in my opinion, the single best place to begin learning about DIY scanners (after the [DIYBookScanner.org home page](http://diybookscanner.org "DIYBookScanner.org: Home Page") and [gallery](http://diybookscanner.org/intro.html "DIYBookScanner.org: Gallery") linked above).
* As noted above, if you are interested in post-processing for your book photos (e.g., white-balance correcting, further automatic cropping, etc., the output from this program can be used as input for **[darktable](http://www.darktable.org/ "darktable")** and **[ScanTailor](http://scantailor.org/ "ScanTailor"),** which are also free and open-source.

# Todo

* Add a command line option (perhaps '-a --list-of-images-with-alert-glyph <file_name>') for scanning workflows that involve occasional non-standard page sizes (for example, for books with inserts). The option would cause the program to behave differently if it recognizes an additional glyph (e.g., glyph #8 in the markers .pdf and .ai files); when the glyph is recognized, the input image name would be appended to a text file. 
    * This would allow a scanner operator to take a picture of the book with image inserts extended, etc., but to temporarily affix the "alert" glyph anywhere near the book, and then remove the glyph and move on to the next pages of the book. This would simplify the process for going back and separately processing those non-standard pages later.
    * This could be defined in a separate function ("is_alert_glyph_present") called from main.cpp near where the page processing function is called for the left and right pages.
* Establish a build process to allow distributing ready-to-use binaries for different platforms (including the major Linux distros 32- and 64-bit, OSX 64-bit, and Windows)
* **Get more people on board!** I (Jacob) am not a C++ developer, so I see my role as primarily custodial right now (i.e., working on documentation and community-building).
    * **Hannes Künsebeck has written [a port of Voussoir to Python called `pyvoussoir`](https://github.com/hnesk/pyvoussoir). That project is likely to be more maintained than this one.**

# Contributors (in reverse-chronological order)

* [Jacob Levernier](http://adunumdatum.org "Jacob Levernier's website")
* [Yutaka Tsutano](http://yutaka.tsutano.com "Yutaka Tsutano's website")
