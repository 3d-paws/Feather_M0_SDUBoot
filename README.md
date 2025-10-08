
### Adding SDUBoot Support for UPDATE.BIN on SD card for Feather M0 boards

- No Change Needed ~/Library/Arduino15/packages/adafruit/hardware/samd/1.7.16/variants/feather_m0/linker_scripts/gcc
- Replace Files in ~/Library/Arduino15/packages/adafruit/hardware/samd/1.7.16/libraries/SDU/extras/SDUBoot \
  with these local files that are tagged with .feather
- Change directory to ~/Library/Arduino15/packages/adafruit/hardware/samd/1.7.16/libraries/SDU/extras/SDUBoot
- Run ./build.sh
- In your project folder create a folder called boot
- Copy ~/Library/Arduino15/packages/adafruit/hardware/samd/1.7.16/libraries/SDU/src/boot/featherm0.h to \
  your project's boot folder

- Add include the SDU.h located in folder boot to the top of your ino file.
<pre>
#include "boot/SDU.h"
</pre>

