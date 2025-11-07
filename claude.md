# TitanPocketKeyboard

## Project Overview

TitanPocketKeyboard is an Android Input Method Editor (IME) specifically designed for the Unihertz Titan Pocket smartphone. It provides a comprehensive keyboard experience that fully utilizes the device's embedded physical QWERTY keyboard, offering features typically found on full-sized keyboards.

## Key Characteristics

- **Platform**: Android (minSdk 29, targetSdk 33)
- **Language**: Kotlin
- **Build System**: Gradle with Kotlin DSL
- **Package**: io.github.oin.titanpocketkeyboard
- **No User Interface**: Operates entirely through the physical keyboard without taking screen space

## Architecture

### Main Components

1. **InputMethodService.kt** (523 lines)
   - Core service extending Android's InputMethodService
   - Handles all key events (onKeyDown, onKeyUp)
   - Manages modifier states (Shift, Alt, Sym)
   - Implements multipress character substitution
   - Provides keyboard navigation features
   - Auto-capitalization logic
   - Status bar icon updates

2. **MultipressController.kt**
   - Manages multipress functionality for accented characters
   - Handles timing thresholds for character substitution
   - Supports multiple language templates

3. **Modifier.kt**
   - Manages modifier key states (Shift, Alt)
   - Implements locking behavior (double-tap to lock)
   - Handles "next key only" modifier activation

4. **SettingsActivity.kt**
   - Provides settings UI using AndroidX Preferences
   - Configurable options for multipress, auto-capitalize, etc.

### Directory Structure

```
titanpocketkeyboard/
├── app/
│   ├── src/main/
│   │   ├── java/io/github/oin/titanpocketkeyboard/
│   │   │   ├── InputMethodService.kt
│   │   │   ├── SettingsActivity.kt
│   │   │   ├── MultipressController.kt
│   │   │   └── Modifier.kt
│   │   ├── res/
│   │   │   ├── drawable/        # Status bar icons (shift, alt, sym variants)
│   │   │   ├── layout/          # Settings activity layout
│   │   │   ├── values/          # Strings, themes, arrays
│   │   │   └── xml/             # Preferences, method config
│   │   └── AndroidManifest.xml
│   └── build.gradle.kts
├── build.gradle.kts
├── settings.gradle.kts
└── README.md
```

## Features

### Core Functionality

1. **Physical Keyboard Integration**
   - Direct mapping of physical keys to characters
   - No on-screen keyboard overlay
   - Full access to all keyboard keys and symbols

2. **Multipress Character Entry**
   - Quick successive key presses produce accented characters
   - Language-specific templates (French, Spanish, German, Portuguese)
   - Configurable templates with ordered character variants
   - Examples:
     - `e` → `é` → `è` → `ê` → `ë` (French)
     - `a` → `á` → `à` → `â` → `ä` → `ã` (French extended)

3. **Long Press Alternate Characters**
   - Hold key for additional symbols and special characters
   - Example: Long press `q` → `0` → `°`
   - Extensive symbol mappings for all keys

4. **Modifier Keys**
   - **Shift**: Capitalizes letters, accesses uppercase variants
   - **Alt**: Accesses alternate character layers
   - **Sym**: Enables keyboard navigation and special functions
   - Double-tap to lock modifier
   - Single tap affects next key only

5. **Sym Modifier Navigation**
   - WASD or HJKL for directional navigation (arrow keys)
   - Y/U/I/O for Home/PageDown/PageUp/End
   - X/C/V for Cut/Copy/Paste
   - Space acts as Shift when Sym is active

6. **Auto-capitalization**
   - Automatically capitalizes first letter of sentences
   - Context-aware (disabled for passwords, emails, URIs)
   - Respects editor input type

7. **Smart Text Features**
   - Two spaces after period → period + space
   - Status bar indicators for modifier states

### Language Support

Optimized for:
- French (primary, QWERTY with French characters)
- English
- Spanish
- Portuguese
- German
- Italian and Dutch (partial support)

## Configuration & Preferences

Settings managed through SharedPreferences:
- `AutoCapitalize`: Enable/disable auto-capitalization (default: true)
- `ModifierLockThreshold`: Time in ms for double-tap lock (default: 250ms)
- `ModifierNextThreshold`: Time in ms for "next key only" (default: 350ms)
- `MultipressThreshold`: Time window for multipress (default: 750ms)
- `UseFirstLevel`: Enable first-level multipress (default: true)
- `DotSpace`: Enable two-space → period+space (default: true)
- `FirstLevelOnlyVowels`: Restrict first-level to vowels only (default: false)
- `FirstLevelTemplate`: Language template selection (default: "fr-ext")

## Key Technical Details

### Event Flow

1. Physical key press generates KeyEvent
2. `onKeyDown()` captures event
3. Check for modifier keys (Shift/Alt/Sym)
4. If Sym active, route to `onSymKey()` for navigation
5. Otherwise, apply multipress substitution via MultipressController
6. If no substitution, use default character from KeyCharacterMap
7. Apply modifier states (Shift/Alt) to character
8. Commit text to InputConnection
9. Update auto-capitalization state
10. Consume "next key only" modifiers
11. Update status bar icon

### Security Considerations

- Properly handles password fields (disables suggestions)
- Vibration permission for haptic feedback
- Direct boot aware for use before device unlock

### Dependencies

```kotlin
implementation("androidx.core:core-ktx:1.9.0")
implementation("androidx.appcompat:appcompat:1.6.1")
implementation("com.google.android.material:material:1.11.0")
implementation("androidx.preference:preference-ktx:1.2.1")
```

## Development Guidelines

### Building

```bash
./gradlew assembleRelease
```

### Installation

APK must be installed on Unihertz Titan Pocket and enabled via:
Settings → System → Languages & input → Virtual keyboard → Manage keyboards

### Customization

To modify keyboard layout or language support:
1. Edit `templates` HashMap in InputMethodService.kt:106-104
2. Add/modify multipress character arrays
3. Update long-press mappings in multipress HashMap
4. Adjust Sym modifier mappings in `onSymKey()` method

### Testing Considerations

- Test on actual Unihertz Titan Pocket hardware (physical keyboard required)
- Verify modifier state transitions
- Test multipress timing across different typing speeds
- Validate auto-capitalization in various editor contexts
- Check status bar icon updates
- Test Sym navigation in different applications

## Important Notes

1. **Hardware Specific**: This IME is specifically designed for the Unihertz Titan Pocket's keyboard layout
2. **No UI**: The application has no traditional user interface besides settings
3. **Language Optimization**: Primary focus is French QWERTY, with support for other Latin-based languages
4. **Status Bar Integration**: Uses Android status bar icons to show modifier states
5. **Vibration Feedback**: Provides haptic feedback for certain key operations

## Future Enhancement Possibilities

- User-customizable key mappings
- Additional language templates
- Sound feedback options
- Per-application settings
- Cloud sync for preferences
- Custom multipress timing per key
- Gesture support on physical keyboard

## License

Mozilla Public License 2.0 (MPL-2.0)
