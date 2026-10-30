# MacBook Pro 15" (A1398) Speaker Mod using A1707 Drivers

Custom speaker retrofit project replacing the fragile (easily torn) and tinny-sounding stock speakers of the **MacBook Pro 15" Retina (A1398, 2012–2015)** with speaker modules from the **MacBook Pro 15" Touch Bar (A1707, 2016–2017)** using custom 3D-printed enclosures and AppleALC audio patching.

> 🌐 **[한국어 설명서 (Korean Guide)](README_KR.md)**

---

## 📌 Supported Models & Compatibility

| Release Era | Model Identifier | Audio Codec | Native Layout | Hardware Fit | KEXT Status |
| :--- | :--- | :--- | :---: | :---: | :---: |
| **Late 2013 ~ Mid 2014** (Haswell) | `MacBookPro11,2`, `11,3` | Cirrus Logic CS4208 | Layout 128 | Compatible ✅ | **Plug & Play (Included Kext) ✅** |
| **Mid 2015** (Haswell Refresh / Broadwell) | `MacBookPro11,4`, `11,5` | Cirrus Logic CS4208 | Layout 128 | Compatible ✅ | **Plug & Play (Included Kext) ✅ (Tested)** |
| **Mid 2012 ~ Early 2013** (Ivy Bridge) | `MacBookPro10,1` | Cirrus Logic CS4206 | Layout 90 / 92 | Compatible ✅ | **Manual Porting & Build Required 🛠️ ([Guide](AppleALC-A1398-Tuned/))** |

> 💡 **Notice for Mid 2012 ~ Early 2013 (MacBookPro10,1):**
> All A1398 generations share the exact same internal speaker enclosure mounting profile (`APN: 923-0660`), meaning the 3D-printed enclosures and hardware drivers fit identically across all models.  
> However, because 2012~Early 2013 models use the CS4206 codec (`Layout 90 / 92`), you must port the DSP profile using the native stock XML files (`layout90`, `layout92`) and build instructions provided in the [`AppleALC-A1398-Tuned`](AppleALC-A1398-Tuned/) directory.

---

## 📊 Frequency Response Comparison

![Measurement Comparison](image/Compare.jpg)
> **Near-field measurement comparison (stock vs. modded, combined response):**
> Low-frequency extension and frequency response comparison.

---

## 🛠 Requirements & BOM

1. **Donor Speaker Drivers:**
   - 1 pair of Left & Right speaker modules from **MacBook Pro 15" Touch Bar (A1707, 2016–2017)**.
2. **Reused Parts from Stock A1398 Speakers:**
   - **Original A1398 speaker wiring & connector assembly** (salvaged directly from the old A1398 speakers — no separate AWG wires required!).
   - **Original vibration damper sponge stickers** from the old A1398 speaker enclosures.
3. **3D Printed Enclosures:**
   - Available in the [`Enclosure/`](Enclosure/) directory:
     - CAD Master Assembly: [`Enclosure.step`](Enclosure/Enclosure.step)
     - Left Parts: `L1.stl`, `L2.stl`, `L3.stl`, `L4.stl`
     - Right Parts: `R1.stl`, `R2.stl`, `R3.stl`, `R4.stl`
4. **Tools & Consumables:**
   - Soldering iron & solder
   - Precision cutters / hobby knife
   - UV resin and 2-part epoxy adhesive (for driver mounting and air-sealing)

---

## 🔨 Step-by-Step Hardware Guide

### Step 1: Disassemble A1707 Speakers & Desolder Tweeters
![Step 1](image/1.png)
- ⚠️ **Caution:** Before separating the drivers, **desolder the tweeter flex cable joints first**. Forcibly prying the unit apart without desoldering will tear and ruin the delicate ribbon cable.

---

### Step 2: Trim the Driver Frame & Expose Copper Pads
| Cut Dimensions | Trimming & Scraping 1 | Trimming & Scraping 2 |
| :---: | :---: | :---: |
| ![Cut Size](<image/cut size.png>) | ![Step 2](image/2.png) | ![Step 3](image/3.png) |
- Trim the plastic housing according to the dimensions shown in [cut size.png](<image/cut size.png>).
- Cut away the designated PCB section as shown in `3.png`, then carefully scrape away the top solder mask (black coating) to expose clean copper pads for wire soldering.

---

### Step 3: Enclosure Mounting & Acoustic Sealing
| Front | Rear |
| :---: | :---: |
| ![Step 4](image/4.png) | ![Step 5](image/5.png) |
- Mount the modified drivers into the 3D-printed enclosure parts using UV resin or epoxy.
- **Tip:** Extract the internal acoustic damping sponge from the original A1707 enclosure and insert it into the new 3D enclosure chamber.
- ⚠️ **Acoustic Seal:** Seal all wire pass-through holes and mating seams with 2-part epoxy. Air leakage will weaken bass response.

---

### Step 4: Wiring with Original A1398 Cable
![Wiring Diagram](image/make.png)
- Salvage the original wiring harness and connector from the old A1398 speakers.
- Solder the wires to the woofer and tweeter pads following the pinout and polarity schematic in [make.png](image/make.png).

---

### Step 5: Final Assembly & Damper Transfer
| Front | Rear |
| :---: | :---: |
| ![Step 6](image/6.png) | ![Step 7](image/7.png) |
- Carefully peel the original sponge damper stickers off the stock A1398 speakers and reattach them onto the exact same locations on the 3D enclosures (prevents rattles and case interference).
- Install the completed assemblies into your A1398 chassis and plug in the connectors.

---

## 💻 Software Configuration (OCLP / OpenCore)

1. **Install `AppleALC.kext`**:
   - Copy the included [`AppleALC.kext`](AppleALC.kext) into your OpenCore EFI folder: `EFI/OC/Kexts/`.
2. **Update `config.plist`**:
   - Add the kext entry to `Kernel -> Add` in your `config.plist`:
   ```xml
   <dict>
       <key>Arch</key>
       <string>x86_64</string>
       <key>BundlePath</key>
       <string>AppleALC.kext</string>
       <key>Comment</key>
       <string>Audio Patch - CS4208 Layout 128</string>
       <key>Enabled</key>
       <true/>
       <key>ExecutablePath</key>
       <string>Contents/MacOS/AppleALC</string>
       <key>MaxKernel</key>
       <string></string>
       <key>MinKernel</key>
       <string></string>
       <key>PlistPath</key>
       <string>Contents/Info.plist</string>
   </dict>
   ```
   > **Note:** `AppleALC.kext` must be loaded **after** `Lilu.kext`.
3. **Boot Argument**:
   - Add `alcid=128` to `NVRAM -> Add -> 7C436110-AB2A-4BBB-A880-FE41995C9F82 -> boot-args`.

---

## 🔬 AppleALC & layout128.xml Patch Details

The [`AppleALC.kext`](AppleALC.kext) in this repository applies the **A1707 (Layout 57) DSP settings onto the native A1398 hardware configuration**:

1. **Preserving Stock A1398 Hardware Communication**: All non-speaker paths (codec identifier, headphone jack sensing, internal microphone, and optical SPDIF) remain stock A1398 settings.
2. **Applying A1707 DSP**: Applied the speaker DSP chain from A1707 (`layout57.xml`) including EQ, compression, delay, chassis resonance suppression, and limiters.
3. **Hardware Adjustments for A1398**:
   - **4-Channel Output Stage (`Dsp4ChOutput`)**: Preserves the native A1398 analog amplifier gain (`Param 4/5 = 0.051`).
   - **Unified Profile ID (`SignalProcessingIDs`)**: Standardized to Profile `0` so the DSP profile remains active regardless of detected driver ID.
4. **CS4208-Only Build**: Excluded unused codecs and pin configurations, compiling exclusively with A1398 CS4208 configuration.

---

## ⚠️ Disclaimer
- This modification involves precision cutting, PCB scraping, soldering, and complete laptop disassembly.
- Proceed at your own risk. The author is not responsible for any damage caused to your hardware.

---

## 📄 License & Credits
- Hardware modification guides and 3D enclosure models are licensed under the [MIT License](LICENSE).
- `AppleALC.kext` and source files are based on the [AppleALC open-source project by Acidanthera](https://github.com/acidanthera/AppleALC) (licensed under BSD 3-Clause), customized with dedicated DSP and pin configurations for A1398.
- Lilu by vit9696.
