
# Adding Tatar Keyboard to AOSP LatinIME — Step‑by‑Step Guide (Beginner Friendly)

This guide explains how to add a **Tatar keyboard layout** to AOSP LatinIME and build/install it.  
It includes two scenarios:

- 💻 Local build on your computer (recommended for most developers)
- 🌐 Build on a remote server

No prior AOSP keyboard experience required.

---

# What We Are Adding

We add 3 things:

1. `kbd_tatar.xml` — keyboard layout
2. `keyboard_layout_set_tatar.xml` — layout set mapping
3. A new `<subtype>` entry in `method.xml` — registers the language

Without step 3 — the keyboard will NOT appear in settings.

---

# Requirements

## OS
- Linux or macOS

## Tools
- AOSP source tree already synced
- adb installed
- JDK installed (version required by your AOSP branch)
- At least one successful AOSP build before

## Disk space
- AOSP tree typically requires 200+ GB

---

# Folder You Will Work In

All keyboard XML files live here:

packages/inputmethods/LatinIME/java/res/xml/

---

# ✅ SCENARIO A — LOCAL COMPUTER (Recommended)

## Step 1 — Open XML Folder

Open in your editor:

packages/inputmethods/LatinIME/java/res/xml/

Use any clean text editor:

- VS Code
- Android Studio
- Sublime
- Notepad++
- nano (terminal only)

Do NOT use Word / Pages / rich text editors.

---

## Step 2 — Create kbd_tatar.xml

Create file:

kbd_tatar.xml

Paste your Tatar keyboard XML layout and save as UTF‑8.

---

## Step 3 — Create keyboard_layout_set_tatar.xml

Create file:

keyboard_layout_set_tatar.xml

Paste:

```xml
<?xml version="1.0" encoding="utf-8"?>
<KeyboardLayoutSet xmlns:latin="http://schemas.android.com/apk/res/com.android.inputmethod.latin">
    <Element latin:elementName="alphabet" latin:elementKeyboard="@xml/kbd_tatar" />
    <Element latin:elementName="symbols" latin:elementKeyboard="@xml/kbd_symbols" />
    <Element latin:elementName="symbolsShifted" latin:elementKeyboard="@xml/kbd_symbols_shift" />
</KeyboardLayoutSet>
```

Save file.

---

## Step 4 — Edit method.xml

Open:

method.xml

Find the section near another Cyrillic language (for example `tyv_RU`).

Add:

```xml
<subtype
    android:imeSubtypeLocale="tt_RU"
    android:languageTag="tt-RU"
    android:imeSubtypeMode="keyboard"
    android:imeSubtypeExtraValue="KeyboardLayoutSet=tatar,AsciiCapable,EmojiCapable"
    android:isAsciiCapable="true"
    android:label="@string/subtype_generic"
    android:icon="@drawable/ic_ime_switcher_dark"
    android:subtypeId="0x52a7c3d1"
/>
```

Save file.

SubtypeId must be unique — any unused hex value is fine.

---

## Step 5 — Build LatinIME

From AOSP root:

```bash
source build/envsetup.sh
lunch aosp_arm64-trunk_staging-eng
m LatinIME
```

---

## Step 6 — Find APK

```bash
ls out/target/product/*/system/product/app/LatinIME/LatinIME.apk
```

---

## Step 7 — Install

```bash
adb install -r LatinIME.apk
adb shell pm clear com.android.inputmethod.latin
adb reboot
```

---

## Step 8 — Enable Keyboard

Settings → Languages & Input → On‑screen keyboard → Android Keyboard → Languages → enable **Tatar (tt‑RU)**

---

# 🌐 SCENARIO B — BUILD ON SERVER

Same XML steps — only editing method differs.

## Edit files on server

Use:

- nano
- vim
- VS Code Remote SSH
- SFTP editor

Example:

```bash
nano kbd_tatar.xml
```

---

## Build on server

```bash
source build/envsetup.sh
lunch aosp_arm64-trunk_staging-eng
m LatinIME
```

---

## Download APK to Mac

On your Mac:

```bash
scp user@server:/path/to/LatinIME.apk .
```

---

## Install from Mac

```bash
adb install -r LatinIME.apk
adb shell pm clear com.android.inputmethod.latin
adb reboot
```

---

# Show Keyboard When Hardware Keyboard Connected

```bash
adb shell settings put secure show_ime_with_hard_keyboard 1
```

---

# Verify APK Contains Tatar Layout

```bash
unzip -l LatinIME.apk | grep tatar
```

You should see:

- kbd_tatar.xml
- keyboard_layout_set_tatar.xml

---

# Common Beginner Mistakes

## Keyboard not visible
→ subtype not added to method.xml

## Installed old APK
→ copied from wrong product folder

## Name mismatch
KeyboardLayoutSet=tatar must match keyboard_layout_set_tatar.xml

## Forgot pm clear
Subtype list is cached

---

# Done

You now added a new language keyboard to AOSP LatinIME.
