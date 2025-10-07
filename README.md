
### Adding SDUBoot Support for UPDATE.BIN on SD card for Feather M0 boards

- No Change Needed ~/Library/Arduino15/packages/adafruit/hardware/samd/1.7.16/variants/feather_m0/linker_scripts/gcc
- Replace Files in ~/Library/Arduino15/packages/adafruit/hardware/samd/1.7.16/libraries/SDU/extras/SDUBoot \
  with these local files that are tagged with .feather
- Change directory to ~/Library/Arduino15/packages/adafruit/hardware/samd/1.7.16/libraries/SDU/extras/SDUBoot
- Run ./build.sh
- In your project folder create a folder called boot
- Copy ~/Library/Arduino15/packages/adafruit/hardware/samd/1.7.16/libraries/SDU/src/boot/featherm0.h to \
  your projecti's boot folder

- Add below lines at the top of you code to add SDUBoot support
<pre>
__attribute__ ((section(".sketch_boot")))
unsigned char sduBoot[0x6000] = {
    #include "boot/featherm0.h"
};
</pre>

