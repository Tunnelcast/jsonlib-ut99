# JsonLib — UT99

A JSON library for UnrealScript on **Unreal Tournament (1999)**, targeting OldUnreal **469**.
Pure script, no native code: build it, add it to `EditPackages`, and you have JSON.

Port of the [UT2004 JsonLib](https://github.com/Tunnelcast/jsonlib-ut2004). Same API, same behaviour.

## What you get

```unrealscript
local JsonObject Json;

Json = new class'JsonObject';
Json.AddString("PlayerName", "Cali");
Json.AddInt("Score", 12);
Json.AddBool("IsSpectator", false);

log(Json.ToString());   // {"PlayerName":"Cali","Score":12,"IsSpectator":false}
```

and back again:

```unrealscript
Json = class'JsonConvert'.static.Deserialize("{\"Score\":12}");
log("" $ Json.GetInt("Score"));   // 12
```

`JsonObject` — `AddString` / `AddInt` / `AddFloat` / `AddBool` / `AddJson` / `AddArray*`,
`GetString` / `GetInt` / `GetFloat` / `GetBool` / `GetArray*` (key lookup is case-insensitive by
default), `RemoveValue`, `Clear`, `ToString`.
`JsonConvert` — `Deserialize`, `DeserializeIntoExistingObject`, `StartsWith`, `EndsWith`.
`JsonUtils` — `HexToInt`, `GetChrCode`.

`AddString` escapes for you: the value is emitted as a JSON string with `\uXXXX` for
anything outside printable ASCII, so the serialized document is always pure ASCII and therefore
always valid UTF-8. Do not pre-filter your text.

## Install

Drop the package next to your mod (a sibling of `System`), and list it **before** your own package:

```ini
[Editor.EditorEngine]
EditPackages=JsonLib
EditPackages=YourMod
```

Build with `ucc make -silent -all -ini=<your>.ini`. `-silent` is not optional on UT99 — without it
UCC blocks forever on an interactive "this function was added in UT v451" prompt.

`JsonObject` is a plain `Object`, so it is server-side only unless you replicate it yourself; it does
not need to go in `ServerPackages`.

## Notes for UE1

The library leans on things UnrealScript 1 is often assumed not to have. It does, on 469: dynamic
arrays (including nested inside a struct), `new class'X'` runtime object instantiation, and array
return-by-value. Two things it genuinely does *not* have, which bite when writing against it:

- A dynamic array does **not** grow on out-of-range assignment. `Arr[Arr.Length] = X` compiles and
  then throws at runtime. Resize first.
- `defaultproperties` cannot populate a dynamic array — do it in code.

The parser is flat: nested objects serialize correctly but are not parsed back into child objects.

## Licence

MIT. Provided as-is, no support guarantee.
