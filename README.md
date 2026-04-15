# Enabling Intel Wireless 3165 Wi-Fi on macOS 26.4 and 26.5 beta 2 Tahoe

https://github.com/kgp-macPro/OCLP-lzhoang2801-amfipassbeta

This thread discusses the experimental OCLP 3.0.0 Nightly fork by lzhoang2801 (development currently on hold), restoring the following functionality on macOS Tahoe 26.x:
 
1.) modern audio (AppleHDA) -- functional  

2.) modern Wi-Fi -- functional  

3.) AWDL stack:

- AirDrop -- fully functional, bidirectional  
- AirPlay -- functional

- Screen Mirroring -- functional
  
4.) Continuity:
  
- Handoff -- functional (e.g. Mail, Notes, Safari)

- Sidecar -- currently not functional  

The fork is not an official upstream OCLP release and is not affiliated with Dortania

Credits
- Dortania OCLP Team
- lzhoang2801
- laobamac_yyds
- YBronst (MakAsrock)
- badbrain
- InsanelyMac community
 
Prerequisites  and Setup:
 
1.) For OCLP 3.0.0 Nightly (amfipassbeta variant) enable AMFIPass.kext and use boot argument "-amfipassbeta".
     For OCLP 3.0.0 Nightly enable or disable AMFIPass.kext but always use boot argument "amfi=0x80".
  
 
2.) Securebootmodel = disabled

 
3.) csr-active-config = 03080000

 
4.) AppleVTD disabled, i.e. DisableIOMapper=true

 
5.) Same com.apple.iokit.IOSkywalkFamily replacement like under Sequoia

 

All settings above (1 to 5) can also be gathered from my EFI-Folder distribution:

The Zip-file contains three EFI-Folders: 1.) EFI-KGP-public-v.1.1.5-BCMC 2.) EFI-KGP-public-v.1.1.5-OCLP-Broadcom 3.) EFI-KGP-public-v.1.1.5-OCLP-Intel

For OCLP 3.0.0 Nightly with Broadcom-Wi-Fi, use EFI-KGP-public-v.1.1.5-OCLP-Broadcom. For OCLP 3.0.0 Nightly with Intel-Wi-Fi, use EFI-KGP-public-v.1.1.5-OCLP-Intel (see update and link at the bottom of this post). 

Happy that it worked out. 👍
I’ve reviewed your EFI and made some adjustments (attached below). I also added all kexts for Intel Bluetooth. 
I also removed your SMBIOS credentials so the EFI can be safely shared publicly.
Feel free to test it and let me know how it works on your system. 👍
 
 
For everybody with Intel W-Fi chipsets: Explanation of the SSDT and DeviceProperties setup
In this setup, it is important to clearly distinguish between the roles of DeviceProperties and the SSDT.
The actual “conversion” to a Broadcom-compatible device is performed via DeviceProperties (Broadcom spoofing). This is the key mechanism that allows macOS to properly attach and initialize the Wi-Fi stack.
The SSDT serves a different purpose. It injects an ARPT device at the correct ACPI path of the Wi-Fi controller and provides macOS-specific properties via _DSM (such as built-in, AAPL,slot-name, device_type, model, and name). This ensures that the device is properly represented within ACPI and IORegistry.
In summary:

-> DeviceProperties → performs the Broadcom spoofing (functional requirement)

-> SSDT → injects ARPT and ensures proper integration and representation (structural requirement)
Both components are required for a clean and working setup.

It is also important to note:

-> The ACPI path is not universal and must be adapted to the individual system (IOREGExplorer)

-> The PCI DeviceProperties path is also not universal and must match the actual hardware layout (Hackintool/PCI)
If either path is incorrect, the setup will not work as expected.
