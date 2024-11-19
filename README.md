1 week
======
jetson-nano setting
-------------------

#SD Card prepare
1. Download the Jetson Nano Developer Kit SD Card Image.
   -https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit#write
2. Format microSD card using SD Memory Card Formatter from the SD Association.
   -Download SD Memory Card Formatter
     https://www.sdcard.org/downloads/formatter/sd-memory-card-formatter-for-windows-download/
   -Select card drive
   -Select “Quick format”
   -Leave “Volume label” blank
   -Click “Format” to start formatting, and “Yes” on the warning dialog
3. Write the image to microSD card 
   -Download Etcher
     https://www.balena.io/etcher
   -Click “Select image” and choose the zipped image file downloaded earlier.
   -Click “Select drive” and choose the correct device.
   -Click “Flash!” It will take Etcher about 10 minutes to write and validate the image

#Boot 

-Connect the USB keyboard and mouse
-Connect Monitor
-Network Dongle
-Insert the microSD card into the slot on the underside of the Jetson Nano module.

#Setup
