# nes_sprite_tools
nes_sprite_tools for manipulate NES ROM files authors: Bob Rost, Joey Echeverria.

 NES Sprite Tools
March 18, 2004

Note:
The Windows versions of these tools require the cygwin1.dll file. This file is available from Bob Rost's NES development resources page.

 

Included tools (and author):
  	bmp2chr	Bob Rost
  	bmp2level	Bob Rost
  	bmp2palette	Bob Rost
  	bmp2sprite	Bob Rost
  	bmp2table	Bob Rost
  	bmpmerge	Bob Rost
  	chr2bmp	Bob Rost
  	sprites2chrsprite	Joey Echeverria

 

General Usage:

These tools convert between various kinds of bitmaps images and other data formats useful in creating games for the NES. Due to the BMP image loader used in these tools, any BMP image used must be 24-bit uncompressed color. This is the standard Windows bitmap output by applications such as Photoshop. The tools themselves may impose further restrictions on size and number of colors allowed in the images. All of these tools are command line applications. If you do not understand how to use them, consult appropriate documentation for using a command line in your computer's operating system.

 

Source Code:

These tools are released with source code into the public domain. They come with no warranty. Each of the included tools makes use of the BMPImage class, written for public domain by Bob Rost. They have been tested under Mac OSX and should work without modification on most popular operating systems. Under standard C++ development environments, you should be able to compile all of the tools with the Makefile.

 

bmp2chr:
This command line application converts a BMP image file into a NES CHR file. The input image size must be 128x128 pixels, and it may contain only the following colors:
 	
Black	 	000000	 	becomes sub-palette color 0
White	 	FFFFFF	 	becomes sub-palette color 1
Red	 	FF0000	 	becomes sub-palette color 2
Blue	 	0000FF	 	becomes sub-palette color 3

Usage: bmp2chr input.bmp output.chr

 

bmp2level:

This tool allows you to input bitmaps with information about your desired level, which can then be automatically converted into usable level data to draw and use the level in a game. The levels currently supported by the tool may be one or two screens wide.

Usage: bmp2level map.bmp pattern.bmp palette.bmp collision.bmp special.bmp output.dat
 	map.bmp 	A full-color screenshot of what your level should look like. The tool supports one or two screens at a time, which means that your image should be either 256x240 pixels, or 512x240 pixels. The tool relies on 16x16 pixel major blocks to create the map, so your map data should consist of sections of that size from your pattern table.
 	pattern.bmp 	A standard 4-color pattern table, as created by bmp2pattern or chr2bmp, or required for bmp2chr. The image should be 128x128 pixels. Colors 0, 1, 2, and 3 map to black, white, red, and blue.
 	palette.bmp 	An image 16 pixels wide. Pixels 0, 4, 8, and 12 must all be the same color, to conform with standard Nintendo color palettes. Only the first row of 16 pixels will be used in order to load the palette.
 	collision.bmp 	An image the same size as your map data. It should consist of 16x16 pixel squares of solid color. Black squares indicate no collision at that point, and white squares indicate collision, meaning that game characters are expected to be able to stand on the square and not go through it.
 	special.bmp 	An image the same size as your map data. Like the collision image, it should consist of 16x16 pixel squares of solid color. The color indicates that a special object of a particular type exists at that location. Each type is simply a number, which should be interpreted by your program. The colors map to type numbers as shown below.
 	black	000000	 	no data
 	white	ffffff	 	1
 	red	ff0000	 	2
 	blue	0000ff	 	3
 	green	00ff00	 	4
 	yellow	ffff00	 	5
 	magenta	ff00ff	 	6
 	cyan	00ffff	 	7
 	output.dat 	The output data file for your level. You may include this file directly in your rom to use in your game. The binary format is as follows:
1024 or 2048 bytes 	Name and Attribute table data to draw the level. If the level is on screen wide, then the 1024 bytes may be copied directly to PPU memory at a nametable starting address, such as $2000, in order to load the nametable and attribute table correctly. If your level is two screens wide, then the first 1024 bytes will be the lefthand screen, and the next 1024 bytes will be the righthand screen.
256 or 512 bytes 	Collision data corresponding to the level. Data is 256 bytes for a one-screen level, or 512 bytes for a two-screen level. Each byte represents a single major block from the collision map, and its value is 0 for passable blocks and 1 for solid blocks. Blocks are addressed in row order from far left to the far right of the map, from top to bottom.
3n +1 bytes
for n special items 	Each 3 bytes represents a single special item. The first byte is the item type, the second byte is the x coordinate, and the third byte is the y coordinate. Coordinates are in major blocks from the upper left of the map, right and downward. The list is null-terminated by a single zero.

 

bmp2palette:

Usage: bmp2palette flag palette.bmp output.dat
flag	optional 	You may optionally specify which system palette to use for designating colors. The available palettes are by Chris Covell, Loopy, and Matthew Conte. If no flag is specified, then Chris Covell's palette is used by default. The flags for each palette are as follows:
-covell
-loopy
-conte
palette.bmp	  	This image should by 16 pixels wide, and either 1 or 2 pixels tall. It may consist of any colors you wish, to be matched to the NES palette. However, every fourth pixel (starting with the upper left pixel) must be the same color, to conform to NES system restrictions.
output.dat	optional 	If you wish, you may specify an output filename. This file will be binary data, 16 or 32 bytes, where each byte is the NES system color of the corresponding pixel, as read in standard row order. If you do not specify an output file, then the colors will output to the console as an nbasic data statement.

 

bmp2sprite:

This tool converts a BMP image file to a NES-style tile layout version. The program does not check for color restrictions.

Usage: bmp2sprite input.bmp output.bmp
input.bmp	An image file that is a multiple of 8x16 pixels.
output.bmp	The output file. It will be a horizontal arrangement of the input file, 8 pixels tall. The 8x8 pixel tiles will be read column order from the input and laid into the output as encountered.

 

bmp2table:

This tool converts a pattern table to a 4-color NES compatible pattern table, based on the provided palette. The user will be alerted of ambiguous palette matches, and it is the user's responsibility to resolve them manually.

Usage: bmp2table input.bmp palette.bmp output.bmp
input.bmp	A 128x128 pixel image in full color.
palette.bmp	An image containing multiple sub palettes, to which the full color image will be matched. The image may contain any number of sub palettes, each one 4x1 pixels. The palette image may be any multiple of 4x1 pixels.
output.bmp	A 128x128 pixel image in NES 4 color, consisting of black, white, red, and blue. This image may be fed to bmp2chr.

 

bmpmerge:

This tool merges two bitmaps. It compares 8x8 tiles. For each tile which is blank (solid color) in one of the input tiles and non-blank in the other, the output file will contain the non-blank tile.

Usage: bmp2table input1.bmp input2.bmp output.bmp
input1.bmp	A 128x128 pixel image.
input2.bmp	A 128x128 pixel image.
output.bmp	A 128x128 pixel image, created as a result of merging the two input pictures.

 

chr2bmp:

This command line application converts a NES CHR file to a BMP image. The input file must be a single-bank CHR ROM (4096 bytes), or else only the first bank will be used. The output image will be a conversion into 4-color space from the NES sub-palette entries, with a size of 128x128 pixels. Palette color 0 becomes black, 1 becomes white, 2 becomes red, and 3 becomes blue.

Usage: chr2bmp input.chr output.bmp
input.chr	
output.bmp	

 

sprites2chrsprite:

Takes a number of input bitmaps and arranges them, in the order provided, into an output pattern table. It does not attempt to alter any colors.

Usage: sprites2chrsprite input1.bmp [input2.bmp [...]] output.bmp
input1.bmp	An image 8 pixels tall, and a multiple of 8 pixels wide.
input2-n.bmp	Optional input images, also 8 pixels tall and a multiple of 8 pixels wide.
output.bmp	A 128x128 pixel pattern table. It will have the same colors as the input files. The input bitmap images will be concatenated row order into the pattern table, in rows of 16 tiles.
