/*
  Copyright (c) 2017 Arduino LLC.  All right reserved.

  This library is free software; you can redistribute it and/or
  modify it under the terms of the GNU Lesser General Public
  License as published by the Free Software Foundation; either
  version 2.1 of the License, or (at your option) any later version.

  This library is distributed in the hope that it will be useful,
  but WITHOUT ANY WARRANTY; without even the implied warranty of
  MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
  See the GNU Lesser General Public License for more details.

  You should have received a copy of the GNU Lesser General Public
  License along with this library; if not, write to the Free Software
  Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA  02110-1301  USA
*/

#include <SD.h>
#include <FlashStorage.h>

#define SDU_START    0x2000
#define SDU_SIZE     0x6000
#define SKETCH_START (uint32_t*)(SDU_START + SDU_SIZE)  // 0x8000: Is where user sketch will run from

#define LORA_SS_PIN   8
#define SDCARD_SS_PIN 10
#define LED_PIN 13
#define UPDATE_FILE "UPDATE.BIN"

FlashClass flash;

// Initialize C library
extern "C" void __libc_init_array(void);

int main() {
  init();
  __libc_init_array();
  delay(1);

  // Turn off FeatherLoRa SPI Select
  pinMode(LORA_SS_PIN, OUTPUT);
  digitalWrite(LORA_SS_PIN, HIGH);

  if (SD.begin(SDCARD_SS_PIN) && SD.exists(UPDATE_FILE)) {
    pinMode(LED_PIN, OUTPUT);
    for (int i = 0; i < 5; i++) {
      digitalWrite(LED_PIN, HIGH);
      delay(100);
      digitalWrite(LED_PIN, LOW);
      delay(100);
    }
    File updateFile = SD.open(UPDATE_FILE);
    uint32_t updateSize = updateFile.size();
    bool updateFlashed = false;

    if (updateSize > SDU_SIZE) {
      // skip the SDU section
      updateFile.seek(SDU_SIZE);
      updateSize -= SDU_SIZE;

      uint32_t flashAddress = (uint32_t)SKETCH_START;

      // erase the pages
      flash.erase((void*)flashAddress, updateSize);

      uint8_t buffer[512];

      // write the pages
      for (uint32_t i = 0; i < updateSize; i += sizeof(buffer)) {
        updateFile.read(buffer, sizeof(buffer));

        flash.write((void*)flashAddress, buffer, sizeof(buffer));

        flashAddress += sizeof(buffer);
      }

      updateFlashed = true;
    }

    updateFile.close();

    if (updateFlashed) {
      SD.remove(UPDATE_FILE);
    }
  }

  // jump to the sketch
  __set_MSP(*SKETCH_START);

  //Reset vector table address
  SCB->VTOR = ((uint32_t)(SKETCH_START) & SCB_VTOR_TBLOFF_Msk);

  // address of Reset_Handler is written by the linker at the beginning of the .text section (see linker script)
  uint32_t resetHandlerAddress = (uint32_t) * (SKETCH_START + 1);
  // jump to reset handler
  asm("bx %0"::"r"(resetHandlerAddress));
}
