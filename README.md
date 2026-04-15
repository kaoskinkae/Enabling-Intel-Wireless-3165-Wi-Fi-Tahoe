# Enabling Intel Wireless 3165 Wi-Fi on macOS 26.4 and 26.5 beta 2 Tahoe

| Pavilion HP2B47 |  i5-6400 CPU @ 3.30GHz Skylake | Intel HD Grafic 530 | imac 17.1  |OpenCore 1.0.7 
| SSD macOS 26 Tahoe | Adapter PCIe 4,0x16 a M.2 4xNVME SomnAmbulist 250G macOS Tahoe beta | Adapter Netac NVME SSD 480 GB Windows 11 25h2 | HDD SATA 3 1T DATE |  Wireless 3165 / BCM_4350C2 

The tests have been successfully performed on this equipment; my guides are always tested before being published.


<img width="881" height="629" alt="Captura de pantalla 2026-04-15 a las 10 07 32" src="https://github.com/user-attachments/assets/ac58604a-8d15-4fbd-9705-4cd6f9abac03" />
<img width="844" height="677" alt="Captura de pantalla 2026-04-15 a las 10 07 52" src="https://github.com/user-attachments/assets/84369d92-6731-4286-a504-bf9e3b70c420" />
<img width="514" height="298" alt="Captura de pantalla 2026-04-15 a las 10 08 07" src="https://github.com/user-attachments/assets/b73ce781-9715-4633-a0b7-c6c1920fbbb0" />


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

<img width="449" height="74" alt="Captura de pantalla 2026-04-15 a las 9 27 51" src="https://github.com/user-attachments/assets/aa83d592-f3a6-4d8f-a286-66ea6ad24ef1" />

3.) csr-active-config = 03080000

<img width="855" height="142" alt="Captura de pantalla 2026-04-15 a las 9 27 29" src="https://github.com/user-attachments/assets/0b708590-798d-4204-b8d7-520688493d1d" />

4.) AppleVTD disabled, i.e. DisableIOMapper=true

<img width="190" height="30" alt="Captura de pantalla 2026-04-15 a las 9 27 39" src="https://github.com/user-attachments/assets/0e647006-c04d-4ab2-b89a-c13090527c77" />

 
5.) Same com.apple.iokit.IOSkywalkFamily replacement like under Sequoia




 <img width="1118" height="98" alt="Captura de pantalla 2026-04-15 a las 9 27 18" src="https://github.com/user-attachments/assets/0e69d9f6-4d14-4dd8-9d9f-ce7ea2a6367d" />


 <img width="1184" height="133" alt="Captura de pantalla 2026-04-15 a las 9 31 34" src="https://github.com/user-attachments/assets/034a9b6a-81ca-49a6-b92f-570e186487a4" />

 <img width="1185" height="197" alt="Captura de pantalla 2026-04-15 a las 9 26 57" src="https://github.com/user-attachments/assets/05607768-c9b4-48fa-880e-f69a0085c1dd" />
 

11 Big Sur Minkernel 20.0.0 MaxKernel 20.99.99

12 monterey Minkernel 21.0.0 MaxKernel 21.99.99

13 ventura Minkernel 22.0.0. MaxKernel 22.99.99

14 sonoma Minkernel 23.0.0. MaxKernel 23.99.99

15 sequoia Minkernel 24.0.0  MaxKernel 24.99.99

16 Tahoa Minkernel 25.0.0. MaxKernel 25.99.99



#All settings above (1 to 5) can also be gathered from my EFI-Folder distribution:

The Zip-file contains three EFI-Folders: 

1.) EFI-KGP-public-v.1.1.5-BCMC 

2.) EFI-KGP-public-v.1.1.5-OCLP-Broadcom 

3.) EFI-KGP-public-v.1.1.5-OCLP-Intel


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

Thanks to kgp of InsanelyMac
