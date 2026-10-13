# Windows Power Management (Chinese Windows)

## Problem: Lid Close Action Not Working

On some Chinese Windows editions, `powercfg /setacvalueindex SCHEME_CURRENT SUB_BUTTONS LIDACTION 0` runs with exit code 0 but the lid-close setting may not actually be present in the active power scheme's SUB_BUTTONS group. Querying `powercfg /query SCHEME_CURRENT SUB_BUTTONS` may only show `UIBUTTON_ACTION` (start menu power button) and not a lid-action entry.

## Fix: Use the LIDACTION GUID Directly

```bash
# Set lid close to "do nothing" when plugged in
powercfg /setacvalueindex SCHEME_CURRENT SUB_BUTTONS 5ca83367-6e45-459f-a27b-476b1d01c936 0

# Set lid close to "do nothing" when on battery  
powercfg /setdcvalueindex SCHEME_CURRENT SUB_BUTTONS 5ca83367-6e45-459f-a27b-476b1d01c936 0

# Apply the active scheme
powercfg /setactive SCHEME_CURRENT
```

**Value meanings:**
- `0` = do nothing (lid close doesn't trigger anything)
- `1` = sleep
- `2` = hibernate
- `3` = shut down

## Also Disable Sleep Timeout

```bash
# Prevent sleep entirely when plugged in (0 = never)
powercfg /change standby-timeout-ac 0

# Prevent hibernate when plugged in
powercfg /change hibernate-timeout-ac 0
```

## Verify Settings

On Chinese Windows, `powercfg /query` output uses GBK encoding. Parsing with Python:
```python
import subprocess
result = subprocess.run(['powercfg', '/query', 'SCHEME_CURRENT', 'SUB_SLEEP'], capture_output=True)
text = result.stdout.decode('gbk', errors='replace')
# Look for "交流电源设置索引: 0x00000000" on the STANDBYIDLE setting
```

## Explanation

The LIDACTION GUID `5ca83367-6e45-459f-a27b-476b1d01c936` is the canonical Windows power setting GUID for "Lid close action" under the `SUB_BUTTONS` subgroup. On some systems the alias `LIDACTION` doesn't resolve, but the GUID works directly.

## npm Mirror Setup (China)

Before using npm-based tools (like `npx skills`), set the Chinese mirror to avoid GFW blocks:
```bash
npm config set registry https://registry.npmmirror.com
```
