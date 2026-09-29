Wardogs / System Performance Backup - Sep 29 2026
==================================================

This kit preserves the "sweet spot" config (snappy + smooth at 144 FPS cap).
Stored in a PRIVATE GitHub repo (wardogs-config). Sensitive keys (Twitch
OAuth tokens, OBS stream key) are intentionally NOT included - keep those
local only.

FILES
-----
powerplan_balanced.pow           Active power plan (Balanced) with:
                                CPU min state 5/5%, USB selective suspend OFF,
                                ASPM, etc. Restore: powercfg /import powerplan_balanced.pow
                                then powercfg /setactive <GUID printed>
powerschemes_all.reg             ALL power schemes (registry)
multimedia_systemprofile.reg     NetworkThrottlingIndex=off, SystemResponsiveness=15,
                                MM CSS "Games" task = Priority 6 / High / High
                                Restore: reg import (Admin)
powerthrottling.reg              PowerThrottlingOff=1
                                Restore: reg import (Admin)
deviceguard.reg                  VBS / Core Isolation OFF
                                Restore: reg import (Admin)
appcompat_fullscreenopt.reg      Wardogs "Disable fullscreen optimizations" flag
                                Restore: reg import (user, no admin needed)
gameconfigstore.reg              GameDVR / FSE behavior
gamebar.reg                      Game Mode / Game Bar state
mouse.reg                        Mouse: pointer precision OFF
amd_cn_hkcu.reg / amd_cn_hklm.reg  AMD Adrenalin settings (TWITCH TOKENS REMOVED)
amd_umd_dgpu.reg                 dGPU (9060 XT) 3D defaults (FsrOverride etc.)
adrenalin_gmdb.blb               Adrenalin per-game database (profiles)
adrenalin_cmdb.blb               Adrenalin component database
GameUserSettings.ini             Wardogs settings: 1440p, FSR3, FrameRateLimit=144,
                                VSync off (current working copy)
GameUserSettings_SEP15_BACKUP.ini  Older copy from Sep 15 (fallback)

NOTE: Adrenalin settings (Anti-Lag ON, FreeSync AMD Optimized, FSR overlay etc.)
are NOT in this kit. Save them via:
  AMD Software -> Settings (gear) -> System -> Export  (keeps per-game profiles)

ADREnalin PER-GAME (WAR DOGS) TO RE-APPLY AFTER ANY RESET
---------------------------------------------------------
  Radeon Anti-Lag  : ON
  Radeon Boost     : OFF
  Radeon Chill     : OFF
  Enhanced Sync    : OFF
  VSync control    : Always Off
  Image Sharpening : OFF
  FreeSync         : AMD Optimized (Display settings)
  HAGS             : ON (Windows: Settings > Display > Graphics)

GAME SETTINGS
-------------
  Resolution : 2560x1440, Upscaling: FSR3 (Quality), FrameRateLimit: 144
  VSync: Off, Display: Windowed/Borderless
  Steam launch option: -nogpucrashdebugging

WINDOWS / SYSTEM (re-apply if a Windows update resets them)
-----------------------------------------------------------
  Power plan: Balanced (this kit's .pow)
  HAGS: ON
  Mouse: Enhance pointer precision OFF, 1000Hz polling
  Fullscreen optimizations: disabled for WardogsClient
  Windows Update: keep paused / re-check before major updates

BIOS (NOT in this kit - set manually, already applied)
------------------------------------------------------
  EXPO/DDR5 @ 6000, iGPU disabled, PCIe ASPM disabled, C-States as chosen

APP NAG / TOOLS (safe to keep running)
--------------------------------------
  Multiplicity (KVM - in use), FanControl, OBS (replay buffer), AutoHotkey (WD QoL)
  Windhawk (classic context menu mod - explorer only, harmless)


===================================================================
FROM-SCRATCH RECREATION CHECKLIST (fresh Windows install)
===================================================================
This is the FULL recipe. The files above only cover the config part;
the steps below cover everything the files CANNOT save.

[1] INSTALL WINDOWS
    - Windows 11 25H2 (UEFI). Do NOT use ReviOS - stock is safer.

[2] BIOS (before/after install)
    - Enable EXPO/DDR5 @ 6000
    - Disable Integrated Graphics (iGPU) - you use the 9060 XT
    - Disable PCIe ASPM
    - (optional) Global C-States off, PBO + Curve Optimizer -15

[3] DRIVERS
    - AMD Chipset driver (latest, from AMD)
    - AMD Adrenalin driver: version 26.10.x / 32.0.31041.1004
      (this kit's registry files assume a compatible driver)

[4] RESTORE CONFIG FILES (in order)
    - powercfg /import powerplan_balanced.pow
      powercfg /setactive <GUID printed>
    - reg import multimedia_systemprofile.reg   (Admin)
    - reg import powerthrottling.reg            (Admin)
    - reg import deviceguard.reg                (Admin)
    - reg import amd_cn_hkcu.reg + amd_cn_hklm.reg  (Admin)  [NO TWITCH TOKENS]
    - reg import amd_umd_dgpu.reg               (Admin)
    - reg import mouse.reg, gamebar.reg, gameconfigstore.reg, appcompat_fullscreenopt.reg
    - Restore GameUserSettings.ini into
      %LOCALAPPDATA%\Wardogs\Saved\Config\WindowsClient\

[5] ADRENALIN (Gaming > War Dogs profile)
    - Radeon Anti-Lag : ON
    - Boost / Chill / Enhanced Sync / Sharpening : OFF
    - VSync control : Always Off
    - FreeSync : AMD Optimized
    - (The gmdb.blb in this kit is a bonus; if it doesn't load cleanly,
      just set the 5 toggles above manually - that's the reliable path.)

[6] WINDOWS SETTINGS
    - HAGS : ON  (Settings > Display > Graphics)
    - Power plan : Balanced
    - Mouse : Enhance pointer precision OFF, 1000Hz polling
    - Display scaling : 100%

[7] STEAM + GAME
    - Install Steam, install WARDOGS
    - Steam > WARDOGS > Properties > Launch Options: -nogpucrashdebugging
    - In-game: 2560x1440, Upscaling FSR3 (Quality), FrameRateLimit 144, VSync Off

[8] VERIFY
    - In a match: GPU 65-90% (not stuck low), CPU at max boost, 144 FPS locked