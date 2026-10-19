# Flashing the Lotus58

How to get a keymap change from this repo onto the keyboard.

## 1. Build the firmware

Every version you flash gets a version number (`v1`, `v2`, ...) and a GitHub Release.

1. Commit and push your keymap changes. GitHub builds them (**Build ZMK firmware** in the **Actions** tab) and redraws the keymap picture (**Draw keymap**).
   - If the build fails, open the run and read the error in the failed step. It usually points at a line in `config/lotus58.keymap`.
2. Run `git pull`. The drawing workflow adds its own commit, so your local copy is one commit behind until you pull.
3. Tag the version and push the tag. Use the next unused number and a short note on what changed:
   ```
   git tag -a v2 -m "ESDF gaming layout"
   git push origin v2
   ```
4. Wait for the **Release firmware** run in the **Actions** tab to finish. It creates a Release on the repo's **Releases** page with the firmware, the keymap picture, and your note.
5. Download the release's `firmware-v2.zip` and unzip it into **`firmware/v2/`** in this repo folder. Git ignores the `firmware/` folder, so each version's files stay local and labelled. `firmware/v1/` holds the first version.

To try something without making a version, skip steps 3–4. Download the **firmware** zip from the Actions run's **Artifacts** instead (these expire after 90 days).

**Going back to an older version:** flash the files from `firmware/<version>/`, or download them again from that version's Release. To keep editing from an old version, check out its keymap with `git checkout v1 -- config/lotus58.keymap`.

The zip contains three files:

| File | What it's for |
|---|---|
| `lotus58_left-nice_view_adapter-nice_view_gem-nice_nano_v2-zmk.uf2` (name may vary slightly) | Left half |
| `lotus58_right-nice_nano_v2-zmk.uf2` | Right half |
| `settings_reset-nice_nano_v2-zmk.uf2` | Wipes saved settings. **Don't use it unless you need to** (see the end of this file) |

## 2. Flash the right half

1. Make sure both halves are switched on and connected to each other. The left half needs to see you holding NAV.
2. Plug the **right** half into the PC with USB.
3. Hold **NAV** (home-row left pinky) and press the **"0" key** on the right half's top number row.
   - From the Gaming layer, NAV is the left middle thumb instead.
4. A drive named **NICENANO** appears.
5. Drag the **`lotus58_right`** `.uf2` onto it.
6. The drive disappears and Windows shows *"Error 0x800701B1: A device which does not exist was specified."* **This is normal.** The board reboots as soon as it has the file. Click **Cancel**.

## 3. Flash the left half

1. Plug the **left** half into the PC with USB.
2. Hold **NAV** (home-row left pinky) and press the **top-left key** (the Esc key on the number row).
3. When **NICENANO** appears, drag the **`lotus58_left`** `.uf2` onto it.
4. Ignore the same Windows error and click **Cancel**.

Always flash **both halves in the same session**. Each half needs its matching file: the right file on the right half and the left file on the left half.

## 4. Check it worked

- Type on both halves. If the right half types, both flashes worked, because the right half only types through the left half.
- The screen on the left half should show the layer name (`BASE`, `SYM`, `NAV`, ...).
- If keys lag, drop or act strangely over Bluetooth, remove the keyboard in Windows Bluetooth settings and pair it again.

## If the bootloader keys don't work

That happens if the firmware on the board is broken or the keymap changed. Other ways in:

- **Reset pins.** On the nice!nano, the `RST` and `GND` pins are next to each other. Short them twice quickly with tweezers to enter bootloader mode. Double-tapping the small switch on the board did nothing, so it's probably the power switch rather than reset.
- **ZMK Studio.** If the board still types, plug in the left half, open <https://zmk.studio> in Chrome, unlock it (hold NAV and press the key at position 44, the left inner key on the bottom row), and check where `&bootloader` is bound.

## `settings_reset`: only when needed

This file clears saved Bluetooth pairings and settings. Use it only if the halves won't connect to each other or Bluetooth stays broken after re-pairing.

**Warning:** after flashing `settings_reset`, that half has **no keymap**, so the bootloader keys won't work on it. To put the real firmware back you'll need the reset pins. Only do this if you can reach them.

If you do use it, for each half:

1. Enter bootloader mode and flash `settings_reset`.
2. Enter bootloader mode again with the reset pins, then flash the normal `lotus58_left` or `lotus58_right` file.
3. Afterwards, remove the keyboard from Windows Bluetooth and pair it again.
