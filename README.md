# BananaTypeFont
A simple file format for saving small pixel fonts
![Example strings](https://i.imgur.com/maqful3.png)

## Provided fonts
BananaTypeFont comes with 3 fonts [here](https://github.com/BananaPuncher714/BananaTypeFont/tree/master/src/main/resources)
- ASCII, the standard 8x8 ascii font
- Default, the default 16x16 font
- Unicode, the unicode variation that is used when force unicode is enabled

## To use within a plugin
* Add the [BananaTypeFont](https://github.com/BananaPuncher714/BananaTypeFont/blob/master/src/main/java/com/aaaaahhhhhhh/bananapuncher714/bananatypefont/BananaTypeFont.java) class to your plugin
* Add the fonts you want to use into your plugin
* On enable, load the fonts like so:
```java
BananaTypeFont defaultFont;
BananaTypeFont asciiFont;
BananaTypeFont unicodeFont;
try {
    defaultFont = BananaFont.from( getResource( "minecraft-font.btf" ) ) );
    asciiFont = BananaFont.from( getResource( "minecraft-font-ascii.btf" ) ) );
    unicodeFont = BananaFont.from( getResource( "minecraft-font-unicode.btf" ) ) );
} catch ( IOException e ) {
    e.printStackTrace();
}
```
* Provide as the font with Bukkit's MapCanvas#drawText method.
```java
public class ExampleRenderer extends MapRenderer {
    BananaTypeFont font;

    @Override
    public void render( MapView mapView, MapCanvas canvas, Player player ) {
        canvas.drawText( 0, 0, font, "Hello, world!" );
    }
}
```

## Format specification
### Version 1
**Header**  
Contains information about the file and font
|**Name**|**Size**|**Description**|
|-|-|-|
|Signature          |2 bytes  |'BF'                       |
|Format             |2 bytes  |1                          |
|Data offset        |2 bytes  |Offset to pixel data       |
|Name length        |1 byte   |Length of the name         |
|Name               |Variable |Name of the font           |
|Version length     |1 byte   |Length of the version      |
|Version            |Variable |Version string             | 
|Description length |1 byte   |Length of the description  |
|Description        |Variable |The description of the font|

**Data**  
Contains an arbitrary amount of character mappings. Each character mapping has a format as follows:
|**Name**|**Size**|**Description**|
|-|-|-|
|Character|2 bytes |Unicode character value|
|Width    |1 byte  |Width of the character |
|Height   |1 byte  |Height of the character|
|Bitmap   |Variable|Data of the character  |  
Each pixel of the character corresponds to a bit in the bitmap. So, if the height and width were 16, the bitmap would consist of 32 bytes. If the total amount of pixels in the character is not divisible by 8, then the last byte is padded with zeros. The first bit corresponds to the bottom left most pixel, and the last bit corresponds to the top right most pixel.