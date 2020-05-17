# YOGA920-Hackintosh
MAC OS Version: 10.15.4 (19E2269)

Thanks @bakgds, @Edd1024, @yisheng9 on tonymacx86

## What is working
* Intel Graphic UHD620
* PS2 Keyboard
* TouchPad and TouchScreen work
* Battery and cpu sensor
* USB: 2/3, type-a/type-c, hot/cold plug, HDMI
* thunderbolt3 maybe
* SSD
* Camera
* Hibernation and wake up
* Wlan Replaced to DW1560
* Bluetooth 4.0 replaced to DW1560 (not very stable)

## What is not working
* WIFI and Bluetooth card change to bcm94352z (DW1560)
* Fingerprint

## SSDT
Follow this [guide](https://www.tonymacx86.com/threads/guide-patching-laptop-dsdt-ssdts.152573/) for extracting your DSDT

* Battery Path
```
into method label B1B2 remove_entry;
into definitionblock code_regex . insert
begin
Method (B1B2, 2, NotSerialized) { Return(Or(Arg0, ShiftLeft(Arg1, 8))) }\n
end;
into method label B1B4 remove_entry;
into definitionblock code_regex . insert
begin
Method (B1B4, 4, NotSerialized)\n
{\n
Store(Arg3, Local0)\n
Or(Arg2, ShiftLeft(Local0, 8), Local0)\n
Or(Arg1, ShiftLeft(Local0, 8), Local0)\n
Or(Arg0, ShiftLeft(Local0, 8), Local0)\n
Return(Local0)\n
}\n
end;
into method label RE1B parent_label H_EC remove_entry;
into method label RECB parent_label H_EC remove_entry;
into device label H_EC insert
begin
Method (RE1B, 1, NotSerialized)\n
{\n
OperationRegion(ERAM, EmbeddedControl, Arg0, 1)\n
Field(ERAM, ByteAcc, NoLock, Preserve) { BYTE, 8 }\n
Return(BYTE)\n
}\n
Method (RECB, 2, Serialized)\n
// Arg0 - offset in bytes from zero-based EC\n
// Arg1 - size of buffer in bits\n
{\n
ShiftRight(Add(Arg1,7), 3, Arg1)\n
Name(TEMP, Buffer(Arg1) { })\n
Add(Arg0, Arg1, Arg1)\n
Store(0, Local0)\n
While (LLess(Arg0, Arg1))\n
{\n
Store(RE1B(Arg0), Index(TEMP, Local0))\n
Increment(Arg0)\n
Increment(Local0)\n
}\n
Return(TEMP)\n
}\n
end;
into device label H_EC code_regex B1TM,\s+16, replace_matched begin TM10,8,TM11,8, end;
into device label H_EC code_regex B1VT,\s+16, replace_matched begin VT10,8,VT11,8, end;
into device label H_EC code_regex B1CR,\s+16, replace_matched begin CR10,8,CR11,8, end;
into device label H_EC code_regex B1RC,\s+16, replace_matched begin RC10,8,RC11,8, end;
into device label H_EC code_regex B1FC,\s+16, replace_matched begin FC10,8,FC11,8, end;
into device label H_EC code_regex B1DC,\s+16, replace_matched begin DC10,8,DC11,8, end;
into device label H_EC code_regex B1DV,\s+16, replace_matched begin DV10,8,DV11,8, end;
into device label H_EC code_regex BDCW,\s+16, replace_matched begin DCW0,8,DCW1,8, end;
into device label H_EC code_regex BDCL,\s+16, replace_matched begin DCL0,8,DCL1,8, end;
into device label H_EC code_regex B1DT,\s+16, replace_matched begin DT10,8,DT11,8, end;
into device label H_EC code_regex B2DT,\s+16, replace_matched begin DT20,8,DT21,8, end;
into device label H_EC code_regex B1CY,\s+16, replace_matched begin CY10,8,CY11,8, end;
into device label H_EC code_regex BCRT,\s+16, replace_matched begin CRT0,8,CRT1,8, end;
into device label H_EC code_regex B1CH,\s+32 replace_matched begin CH10,8,CH11,8,CH12,8,CH13,8 end;
into device label H_EC code_regex (BARL,)\s+(64) replace_matched begin BARZ,%2,//0x06 end;
into device label H_EC code_regex (BARH,)\s+(64) replace_matched begin BARY,%2,//0x0E end;
into device label H_EC code_regex (B1MA,)\s+(64) replace_matched begin B1MX,%2,//0x80 end;
into device label H_EC code_regex (B1DN,)\s+(64) replace_matched begin B1DX,%2,//0x88 end;
into device label H_EC code_regex (SMD0,)\s+(256) replace_matched begin SMDY,%2,//0x1C end;
into method label GSBI code_regex \(B1TM, replaceall_matched begin (B1B2(TM10,TM11), end;
into method label GSBI code_regex \(B1VT, replaceall_matched begin (B1B2(VT10,VT11), end;
into method label _BST code_regex ECRD\s\(RefOf\s\(B1VT\)\) replaceall_matched begin B1B2(VT10,VT11) end;
into method label GSBI code_regex \(B1CR, replaceall_matched begin (B1B2(CR10,CR11), end;
into method label _BST code_regex ECRD\s\(RefOf\s\(B1CR\)\) replaceall_matched begin B1B2(CR10,CR11) end;
into method label GSBI code_regex \(B1RC, replaceall_matched begin (B1B2(RC10,RC11), end;
into method label _BST code_regex ECRD\s\(RefOf\s\(B1RC\)\) replaceall_matched begin B1B2(RC10,RC11) end;
into method label GSBI code_regex \(B1FC, replaceall_matched begin (B1B2(FC10,FC11), end;
into method label _BIF code_regex ECRD\s\(RefOf\s\(B1FC\)\) replaceall_matched begin B1B2(FC10,FC11) end;
into method label GSBI code_regex \(B1DC, replaceall_matched begin (B1B2(DC10,DC11), end;
into method label _BIF code_regex ECRD\s\(RefOf\s\(B1DC\)\) replaceall_matched begin B1B2(DC10,DC11) end;
into method label GSBI code_regex \(B1DV, replaceall_matched begin (B1B2(DV10,DV11), end;
into method label _BIF code_regex ECRD\s\(RefOf\s\(B1DV\)\) replaceall_matched begin B1B2(DV10,DV11) end;
into method label GSBI code_regex \(BDCW, replaceall_matched begin (B1B2(DCW0,DCW1), end;
into method label GSBI code_regex \(BDCL, replaceall_matched begin (B1B2(DCL0,DCL1), end;
into method label GSBI code_regex \(B1DT, replaceall_matched begin (B1B2(DT10,DT11), end;
into method label GSBI code_regex \(B2DT, replaceall_matched begin (B1B2(DT20,DT21), end;
into method label GBID code_regex \(B1CY, replaceall_matched begin (B1B2(CY10,CY11), end;
into method label SMTF code_regex \(BCRT, replaceall_matched begin (B1B2(CRT0,CRT1), end;
into method label GSBI code_regex \(B1CH, replaceall_matched begin (B1B4(CH10,CH11,CH12,CH13), end;
into method label GSBI code_regex \(B1DN, replaceall_matched begin (RECB(0x88,64), end;
into method label GSBI code_regex \(B1MA, replaceall_matched begin (RECB(0x80,64), end;
into method label _BIF code_regex \(B1MA, replaceall_matched begin (RECB(0x80,64), end;
into method label GSBI code_regex \(BARL, replaceall_matched begin (RECB(0x06,64), end;
into method label GSBI code_regex \(BARH, replaceall_matched begin (RECB(0x0E,64), end;
into method label BATD code_regex ECRD\s\(RefOf\s\(\^\^PCI0\.LPCB\.H\_EC\.BARN\)\) replaceall_matched begin RECB(0x1C,256) end;
```

* Spoiler: Brightness Keys Patch
```
into method label _Q38 replace_content
begin
Notify (PS2K, 0x0406)
end;
into method label _Q39 replace_content
begin
Notify (PS2K, 0x0405)
end;
```

### sleep
```
sudo pmset -a hibernatemode 0
sudo rm /var/vm/sleepimage
sudo mkdir /var/vm/sleepimage
sudo pmset -a standby 0
sudo pmset -a autopoweroff 0
```
