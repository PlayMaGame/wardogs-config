# Wardogs — system performance backup

**Sep 29 2026.** This kit preserves the "sweet spot" config: snappy and smooth at a
144 FPS cap.

Sensitive keys (Twitch OAuth tokens, OBS stream key) are intentionally **not**
included — keep those local only.

## Files

| File | What it is |
|---|---|
| `powerplan_balanced.pow` | Active power plan (Balanced) with CPU min state 5/5%, USB selective suspend off, ASPM. Restore: `powercfg /import powerplan_balanced.pow`, then `powercfg /setactive <GUID printed>` |
| `powerschemes_all.reg` | All power schemes |
| `multimedia_systemprofile.reg` | `NetworkThrottlingIndex=off`, `SystemResponsiveness=15`, MM CSS "Games" task = Priority 6 / High / High |
| `powerthrottling.reg` | `PowerThrottlingOff=1` |
| `deviceguard.reg` | VBS / Core Isolation off |
| `appcompat_fullscreenopt.reg` | Wardogs "disable fullscreen optimizations" flag (user, no admin needed) |
| `gameconfigstore.reg` | GameDVR / fullscreen-exclusive behaviour |
| `gamebar.reg` | Game Mode / Game Bar state |
| `mouse.reg` | Mouse: enhance pointer precision off |
| `amd_cn_hkcu.reg`, `amd_cn_hklm.reg` | AMD Adrenalin settings (Twitch tokens removed) |
| `amd_umd_dgpu.reg` | dGPU (9060 XT) 3D defaults, e.g. `FsrOverride` |
| `adrenalin_gmdb.blb` | Adrenalin per-game database (profiles) |
| `adrenalin_cmdb.blb` | Adrenalin component database |
| `GameUserSettings.ini` | Wardogs settings: 1440p, FSR3, `FrameRateLimit=144`, VSync off |
| `GameUserSettings_SEP15_BACKUP.ini` | Older copy from Sep 15 (fallback) |

Adrenalin settings (Anti-Lag on, FreeSync AMD Optimized, FSR overlay) are **not**
in this kit. Save them via AMD Software → Settings (gear) → System → Export, which
keeps per-game profiles.

## Adrenalin per-game (WARDOGS) — re-apply after any reset

| Setting | Value |
|---|---|
| Radeon Anti-Lag | On |
| Radeon Boost | Off |
| Radeon Chill | Off |
| Enhanced Sync | Off |
| VSync control | Always off |
| Image Sharpening | Off |
| FreeSync | AMD Optimized (display settings) |
| HAGS | On |

## Game settings

- Resolution 2560x1440, upscaling FSR3 (Quality), `FrameRateLimit: 144`
- VSync off, windowed/borderless
- Steam launch option: `-nogpucrashdebugging`

## Windows / system — re-apply if an update resets them

- Power plan: Balanced (this kit's `.pow`)
- HAGS: on
- Mouse: enhance pointer precision off, 1000 Hz polling
- Fullscreen optimizations: disabled for `WardogsClient`
- Windows Update: keep paused, re-check before major updates

## BIOS — set manually, not in this kit

EXPO/DDR5 @ 6000, iGPU disabled, PCIe ASPM disabled, C-States as chosen.

## Tools safe to keep running

Multiplicity (KVM, in use), FanControl, OBS (replay buffer), AutoHotkey (Wardogs
QoL), Windhawk (classic context menu — explorer only, harmless).

---

# From-scratch recreation checklist (fresh Windows install)

This is the full recipe. The files above only cover the config part; these steps
cover everything the files *cannot* save.

## 1. Install Windows

Windows 11 25H2 (UEFI). Do not use ReviOS — stock is safer.

## 2. BIOS (before or after install)

- Enable EXPO/DDR5 @ 6000
- Disable integrated graphics — you use the 9060 XT
- Disable PCIe ASPM
- Optional: global C-States off, PBO + Curve Optimizer −15

## 3. Drivers

- AMD Chipset driver (latest, from AMD)
- AMD Adrenalin 26.10.x / 32.0.31041.1004 — the registry files here assume a
  compatible driver

## 4. Restore config files, in order

```bat
powercfg /import powerplan_balanced.pow
powercfg /setactive <GUID printed>
reg import multimedia_systemprofile.reg   :: Admin
reg import powerthrottling.reg            :: Admin
reg import deviceguard.reg                :: Admin
reg import amd_cn_hkcu.reg                :: Admin, no Twitch tokens
reg import amd_cn_hklm.reg                :: Admin, no Twitch tokens
reg import amd_umd_dgpu.reg               :: Admin
reg import mouse.reg
reg import gamebar.reg
reg import gameconfigstore.reg
reg import appcompat_fullscreenopt.reg
```

Then restore `GameUserSettings.ini` into
`%LOCALAPPDATA%\Wardogs\Saved\Config\WindowsClient\`.

## 5. Adrenalin, Gaming → War Dogs profile

Anti-Lag on; Boost, Chill, Enhanced Sync and Sharpening off; VSync control Always
off; FreeSync AMD Optimized.

The `gmdb.blb` here is a bonus — if it doesn't load cleanly, set the five toggles
by hand. That's the reliable path.

## 6. Windows settings

- HAGS: on (Settings → Display → Graphics)
- Power plan: Balanced
- Mouse: enhance pointer precision off, 1000 Hz polling
- Display scaling: 100%

## 7. Steam + game

Install Steam, install WARDOGS, set launch options to `-nogpucrashdebugging`.

In game: 2560x1440, FSR3 (Quality), frame rate limit 144, VSync off.

## 8. Verify

In a match: GPU 65–90% (not stuck low), CPU at max boost, 144 FPS locked.

---

No license. Personal config, published because the checklist might save someone
an evening.
