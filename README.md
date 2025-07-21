# Important Notes
- **This script is exclusively for Apple Silicon MacBooks** where keyboard brightness function keys have been removed and cannot be remapped through System Settings.
- If you've already configured custom _hidutil_ key bindings, please see the [Manual Configuration](#manual-configuration) section.

## What Does This Tool Do?
This script restores the keyboard brightness controls (up/down) to the function key row, just like on older Intel MacBooks.

The tool uses **hidutil**, Apple's key remapping utility introduced in macOS 10.12 Sierra alongside the **IOHIDEventSystemClient API**. For more details, see [Apple's Technical Note TN2450](https://developer.apple.com/library/archive/technotes/tn2450/_index.html#//apple_ref/doc/uid/DTS40017618-CH1-KEY_TABLE_USAGES).

## Available Mapping Options
You can choose from three different key positions:

- **Classic (F5 & F6)**: Maps to the same position as older MacBooks
  - Replaces: Dictation (F5) and Focus Mode (F6)
- **Mid (F4 & F5)**: A compromise between the other options  
  - Replaces: Spotlight (F4) and Dictation (F5)
- **Left (F3 & F4)**: Places controls next to screen brightness keys
  - Replaces: Launchpad (F3) and Spotlight (F4)

> **Note**: Other function keys (screen brightness, media controls, volume) aren't included as mapping options since they're used more frequently. For custom configurations, see [Manual Configuration](#manual-configuration).

## How to use
### Script (Recommended)
1. Clone the repository: `$ git clone https://github.com/ZoCrit/kbx.git`
   - Alternatively, download and extract the ZIP file manually
2. Navigate to the project directory: `$ cd kbx`
3. Make the script executable: `$ chmod +x kbx`
4. Run the script: `$ ./kbx`
5. Choose your preferred keyboard brightness mapping by entering a number (1-3)
   - Press `0` to exit the script
   - [Screenshots](#screenshots)

## Manual Configuration
If you prefer to set up the key remapping manually instead of using the script:

### Installing a Configuration
1. Create the LaunchAgents directory if it doesn't exist:
   ```bash
   sudo mkdir -p /Library/LaunchAgents
   ```
2. Create the configuration file:
   ```bash
   sudo touch /Library/LaunchAgents/com.local.KeyRemapping.plist
   ```
3. Open the file in your preferred text editor (with sudo privileges)
4. Copy and paste the plist content for your desired configuration from [Plist Configurations](#plist-configurations)
5. Save the file and load the configuration:
   ```bash
   sudo launchctl load /Library/LaunchAgents/com.local.KeyRemapping.plist
   ```
6. Apply the mapping immediately using the terminal command from [Terminal Commands](#terminal-commands)

### Removing a Configuration
1. Unload and delete the configuration file:
   ```bash
   sudo launchctl unload /Library/LaunchAgents/com.local.KeyRemapping.plist
   sudo rm /Library/LaunchAgents/com.local.KeyRemapping.plist
   ```
2. Clear any active key mappings:
   ```bash
   hidutil property --set '{"UserKeyMapping":[]}'
   ```

### Plist Configurations
### Plist Configurations

#### Classic Configuration (F5 & F6)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.local.KeyRemapping</string>
    <key>ProgramArguments</key>
    <array>
        <string>/usr/bin/hidutil</string>
        <string>property</string>
        <string>--set</string>
        <string>{"UserKeyMapping":[
            {
              "HIDKeyboardModifierMappingSrc": 0xC000000CF,
              "HIDKeyboardModifierMappingDst": 0xFF00000009
            },
            {
              "HIDKeyboardModifierMappingSrc": 0x10000009B,
              "HIDKeyboardModifierMappingDst": 0xFF00000008
            }
        ]}</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
</dict>
</plist>
```

#### Mid Configuration (F4 & F5)  
Use the Classic configuration above, but change these hex values:
- `0xC000000CF` → `0xC00000221`
- `0x10000009B` → `0xC000000CF`

#### Left Configuration (F3 & F4)  
Use the Classic configuration above, but change these hex values:
- `0xC000000CF` → `0xFF0100000010`
- `0x10000009B` → `0xC00000221`
    
### Terminal Commands
Use these commands to apply the key mappings immediately (useful for testing):

#### Classic Configuration (F5 & F6)
```bash
hidutil property --set '{"UserKeyMapping":[
  {
    "HIDKeyboardModifierMappingSrc": 0xC000000CF,
    "HIDKeyboardModifierMappingDst": 0xFF00000009
  },
  {
    "HIDKeyboardModifierMappingSrc": 0x10000009B,
    "HIDKeyboardModifierMappingDst": 0xFF00000008
  }
]}'
```
    
#### Mid Configuration (F4 & F5)
```bash
hidutil property --set '{"UserKeyMapping":[
  {
    "HIDKeyboardModifierMappingSrc": 0xC00000221,
    "HIDKeyboardModifierMappingDst": 0xFF00000009
  },
  {
    "HIDKeyboardModifierMappingSrc": 0xC000000CF,
    "HIDKeyboardModifierMappingDst": 0xFF00000008
  }
]}'
```

#### Left Configuration (F3 & F4)
```bash
hidutil property --set '{"UserKeyMapping":[
  {
    "HIDKeyboardModifierMappingSrc": 0xFF0100000010,
    "HIDKeyboardModifierMappingDst": 0xFF00000009
  },
  {
    "HIDKeyboardModifierMappingSrc": 0xC00000221,
    "HIDKeyboardModifierMappingDst": 0xFF00000008
  }
]}'
```