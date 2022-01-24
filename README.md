# INI Parser
INI Parser (`ini_parser.lua`) is a modular utility to parse your INI files. It features:
  - A simple object-oriented approach to configuration IO.
  - Significantly better readability as opposed to base Lua IO. 
  - A collection of some neat and small additions that extend the avaliable IO.
    - One example of this is added support for relative imports for `ini.parse`. It's not huge, just neat.
  - Good performance, being able to parse most files in 1 millisecond, and larger files in similar times.

#### How does it work?
- It will load your whole file into memory and recurse through it, line-by-line.
- The cache checks to see if this line was processed before, if it was, return the results.
- If it wasn't processed, each line is pressed against a key-value regex to extract the potential key and value.
- The cache is checked to see if the same raw key or value was processed anywhere else in the file.
- If it was, the cache returns the value, which avoids reprocessing things we've seen before.
- If not, then we serialize the key/value, and store it in the cache.
- After the key and value are serialized, we store this line in the cache and map it to the serialized key and value.

This is a particuarly memory heavy operation, but that was the sacrifice to ensure good execution times. All the garbage is eventually cleaned up, and you can clean it up immediately by calling `collectgarbage()` after you parse your file.

## INI Parser Information
- `function ini.parse(path, cwd)` — Parses an INI file @ cwd .. path ::
  - `cwd` is an optional parameter to define a CWD other than `2Take1Menu\\scripts\\`. It must be an absolute path.
  - **Returns:**
    - Table:
      - `function Table.save(_path, skip_keys)`
        - Saves this INI configuration. 
          - `_path` is an optional path to specify an alternative file to save as. It needs to be absolute.
          - `skip_keys` is an array of keys to not save.
<br/><br/>
- `function ini.match(path, key_name, desired_value)` — Match key_name=desired_value from the file @ `path`
  - `path` specifies the path of the file.
  - `key_name` specifies the key to match against.
  - `desired_value` specifies the value to match against.
  - **Returns:**
      - A boolean indicating if "key_name"="desired_value".
<br/><br/>
- `function ini._Case_Ini_Recurse(tab, tab_name)` — [DEBUG]: Recursively prints the contents of a table ::
  - `tab` specifies the table to recurse upon.
  - `tab_name` is an optional parameter to specify the name of the table being recursed upon.

### INI Parser Usage
Example 1:
```lua
local ini = require "./ini_parser"
local cfg = ini.parse "my_ini.ini"

local function input(msg)
    local a 
    repeat 
        io.write(msg)
        io.flush()
        a = io.read()
    until a

    return a
end

cfg.username = input "What is your name?"
cfg.color.fav = input "On a side note, what's your favorite color?"
cfg.other.age = input "Also, what is your age?"
cfg.save()
```
```ini
; my_ini.ini — The simple script above produces the following result:
username=Ryan
[color]
fav=red
[other]
age=99
```
Example 2:
```lua
local ini = require "./ini_parser"
local cfg = ini.parse "my_ini.ini"

print("Hello there, " .. cfg.username .. "!\n")
print("Your favorite color is " .. cfg.color.fav .. ", and you're " .. tostring(cfg.other.age) .. "!")
```

#### INI Parser, Further Information
- Booleans and numbers are automatically converted into Lua values from INI. 
- If a key can't be accessed syntacticly (i.e, like.this), use["this format"].
- Spaces trailing your section \[brackets] will prevent it from being registered. 

# Suggestions
- I am open to suggestions of improvements or scripts. Create an issue and I'll respond in a timely manner. 
