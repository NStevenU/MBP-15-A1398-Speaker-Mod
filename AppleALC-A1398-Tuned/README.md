# AppleALC for MacBook Pro Retina 15" (A1398, Late 2013 ~ Mid 2015) - Custom DSP Tuned

A lightweight, dedicated build of **AppleALC** specifically tailored for **MacBook Pro 15" Retina (A1398, Late 2013 ~ Mid 2015)** models.

This repository integrates an advanced 16-step DSP audio processing chain ported from the 15-inch Touch Bar MacBook Pro (`A1707`, Layout 57) directly into the native **Cirrus Logic CS4208 (Layout 128)** audio profile. It provides punchy bass, flat midrange, and pristine clarity without causing hardware clipping or kernel panics.

---

## 🎯 Target Hardware & Compatibility

| Model | Model Identifier | Audio Codec | Native Layout | Supported? |
| :--- | :--- | :--- | :---: | :---: |
| **Mid 2015** | `MacBookPro11,4` / `11,5` | Cirrus Logic CS4208 | **128** | ✅ **Verified & Tested** |
| **Late 2013 ~ Mid 2014** | `MacBookPro11,2` / `11,3` | Cirrus Logic CS4208 | **128** | ✅ **Supported** (Same Codec/HDA Layout) |
| **Mid 2012 ~ Early 2013** | `MacBookPro10,1` | Cirrus Logic CS4206 | **90 / 92** | ⚠️ **Not Tested** (See notice below) |

> ### ⚠️ Notice regarding Mid 2012 ~ Early 2013 (MacBookPro10,1)
> Although the physical internal speaker enclosure shape (`APN: 923-0660`) is identical across all A1398 models, **Mid 2012 ~ Early 2013 models use a different audio codec (CS4206) and a different layout ID (`Layout 90 / 92`)**.
> 
> For convenience, the following XML files are provided at the root of this repository:
> - `layout128_Tuned.xml`: The custom tuned 16-step DSP layout used by this repository.
> - `layout90_Stock_2012-2013.xml`: The native stock layout for 2012~Early 2013 MacBookPro10,1.
> - `layout92_Stock_2012-2013.xml`: The alternate native stock layout for 2012~Early 2013.
> 
> If you wish to port this DSP tuning to a 2012 ~ Early 2013 model:
> 1. Open `layout90_Stock_2012-2013.xml`.
> 2. Replace the `IntSpeaker > SignalProcessing` block with the one from `layout128_Tuned.xml`.
> 3. Verify the final output amplifier gain parameters (`Dsp4ChOutput`) match your board's amplifier.

---

## 🛠️ Modifications in this Repository

1. **Ultra-lightweight footprint**:
   - Stripped away 110+ unused PC Hackintosh audio codec profiles.
   - Retained only **Cirrus Logic CS4208 (Layout 128)** and its required PinConfigs.
   - Compiled binary size is only **~86 KB**.
2. **Correct Hardware Revision**:
   - Fixed codec revision detection by mapping `0x100300` (`1049344`), preventing the AppleALC enabler from skipping genuine MacBook Pro hardware.
3. **Native Platforms Pass-through**:
   - Leaves the `Platforms` array empty so macOS `AppleHDA` uses its native, stable platform topology without injection conflicts.
4. **Non-destructive injection**:
   - Injected on-the-fly via OpenCore / OpenCore Legacy Patcher (OCLP) at boot time.
   - **Does NOT modify or break macOS Signed System Volume (SSV)**—your speaker tuning persists across macOS system updates!

---

## 🚀 How to Build

### Requirements
- macOS with Xcode 15 or 16 installed (`xcode-select -s /Applications/Xcode.app/Contents/Developer`)

### Build Steps
```bash
git clone https://github.com/<your-username>/AppleALC-A1398-Tuned.git
cd AppleALC-A1398-Tuned

# 1. Pack layouts and generate C++ resources
./Tools/zlib_pack.command

# 2. Build Release target
xcodebuild -project AppleALC.xcodeproj -target AppleALC -configuration Release
```
The compiled kext will be located at `build/Release/AppleALC.kext`.

---

## 💻 Installation (OpenCore / OCLP)

1. Mount your EFI partition.
2. Copy `AppleALC.kext` to `EFI/OC/Kexts/`.
3. Add `AppleALC.kext` to `config.plist` under `Kernel -> Add` (placed after `Lilu.kext`).
4. Add `alcid=128` to your `NVRAM -> Add -> 7C436110-AB2A-4BBB-A880-FE41995C9F82 -> boot-args`.
5. Reboot your Mac.

---

## 📄 License & Credits
- Original AppleALC by **Acidanthera** (Licensed under BSD-3-Clause).
- Lilu by **vit9696**.
- Custom A1398 CS4208 DSP Tuning & Porting by **NSteven**.
