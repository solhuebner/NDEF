# NDEF Library for Arduino

**Based on work of Don, ifullgaz, eecharlie, JiapengLi, the-real-orca, hirotakaster and countless more.**

Read and Write NDEF messages on NFC Tags with Arduino.

NFC Data Exchange Format (NDEF) is a common data format that operates across all NFC devices, regardless of the underlying tag or device technology.

### Supports 
 - Reading from Mifare Classic Tags with 4 byte UIDs.
 - Writing to Mifare Classic Tags with 4 byte UIDs.
 - Reading from Mifare Ultralight tags.
 - Writing to Mifare Ultralight tags.
 - Peer to Peer
 - I2C NTAG

### Requires

[PN532 Library](https://github.com/solhuebner/PN532)

## Getting Started

To use the Ndef library in your code, include the following in your sketch

For I2C 

    #include <Wire.h>
    #include <PN532_I2C.h>
    #include <PN532.h>
    #include <NfcAdapter.h>
    
    PN532_I2C pn532_i2c(Wire);
    NfcAdapter nfc = NfcAdapter(pn532_i2c);

For SPI

    #include <SPI.h>
    #include <PN532_SPI.h>
    #include <PN532.h>
    #include <NfcAdapter.h>
    
    PN532_SPI pn532spi(SPI, 10);
    NfcAdapter nfc = NfcAdapter(pn532spi);

For HSU

    #include <PN532_HSU.h>
    #include <PN532.h>
    #include <NfcAdapter.h>
    
    PN532_HSU pn532hsu(Serial1);
    NfcAdapter nfc = NfcAdapter(pn532hsu);

### NfcAdapter

The user interacts with the NfcAdapter to read and write NFC tags using the NFC shield.

Read a message from a tag

    if (nfc.tagPresent()) {
        NfcTag tag = nfc.read();
        tag.print();
    }

Write a message to a tag

    if (nfc.tagPresent()) {
        NdefMessage message = NdefMessage();
        message.addTextRecord("Hello, Arduino!");
        success = nfc.write(message);
    }

Erase a tag. Tags are erased by writing an empty NDEF message. Tags are not zeroed out the old data may still be read off a tag using an application like [NXP's TagInfo](https://play.google.com/store/apps/details?id=com.nxp.taginfolite&hl=en).

    if (nfc.tagPresent()) {
        success = nfc.erase();
    }


Format a Mifare Classic tag as NDEF.

    if (nfc.tagPresent()) {
        success = nfc.format();
    }


Clean a tag. Cleaning resets a tag back to a factory-like state. For Mifare Classic, tag is zeroed and reformatted as Mifare Classic (non-NDEF). For Mifare Ultralight, the tag is zeroed and left empty.

    if (nfc.tagPresent()) {
        success = nfc.clean();
    }


### NfcTag 

Reading a tag with the shield, returns a NfcTag object. The NfcTag object contains meta data about the tag UID, technology, size. When an NDEF tag is read, the NfcTag object contains a NdefMessage.

### NdefMessage

A NdefMessage consist of one or more NdefRecords.

The NdefMessage object has helper methods for adding records.

    ndefMessage.addTextRecord("hello, world");
    ndefMessage.addUriRecord("http://arduino.cc");

The NdefMessage object is responsible for encoding NdefMessage into bytes so it can be written to a tag. The NdefMessage also decodes bytes read from a tag back into a NdefMessage object.

### NdefRecord

A NdefRecord carries a payload and info about the payload within a NdefMessage.

### Peer to Peer

Peer to Peer is provided by the LLCP and SNEP support in the [PN532 library](https://github.com/solhuebner/PN532). P2P requires SPI and has only been tested with the Seeed Studio shield. Peer to Peer was tested between Arduino and Android or BlackBerry 10. See [P2P_Send](examples/P2P_Send/P2P_Send.ino) and [P2P_Receive](examples/P2P_Receive/P2P_Receive.ino) for more info.

### Specifications

This code is based on the "NFC Data Exchange Format (NDEF) Technical Specification" and the "Record Type Definition Technical Specifications" that can be downloaded from the [NFC Forum](http://www.nfc-forum.org/specs/spec_license).

### Tests

To run the tests, you'll need [ArduinoUnit](https://github.com/mmurdoch/arduinounit). To "install", I clone the repo to my home directory and symlink the source into ~/Documents/Arduino/libraries/ArduinoUnit.

    $ cd ~
    $ git clone git@github.com:mmurdoch/arduinounit.git
    $ cd ~/Documents/Arduino/libraries/
    $ ln -s ~/arduinounit/src ArduinoUnit
    
Tests can be run on an Uno without a NFC shield, since the NDEF logic is what is being tested.
    
## Warning

This software is in development. Error handling could use improvement. It runs out of memory, especially on the Uno board. Use small messages with the Uno. The Due board can write larger messages. Please submit patches.

## TODO

Include Type 4 support  
https://github.com/aligot-cblue/NDEF/commit/14b9b9a3878bed15813c3c7f6ba446b125c3feff
https://github.com/aligot-cblue/NDEF/commit/804949df8e34333cb895076261dd0bfa5ccc38f1

Style cleanup?  
https://github.com/henrycjc/EasiNDEF/commits/master

Mem leak fixes?  
https://github.com/ifullgaz/NDEF/commit/610e2593f85f9575ecab79ae9f837aff1b3cd044?branch=610e2593f85f9575ecab79ae9f837aff1b3cd044&diff=unified

In case of STM32 issues?  
https://github.com/LieBtrau/NDEF/commits/master
