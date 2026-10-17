# Part Sourcing and Library Management (LCSC / JLCPCB)

How parts for this project are picked and how their symbols, footprints and 3D models
end up in the project-local `lib/` folder.

## Part sourcing

- [JLCPCB Parts](https://jlcpcb.com/parts) — check the assembly stock here. Parts marked
  `Extended` cost about 3 EUR loading fee each, so prefer `Basic` / `Preferred` parts
  where it does not hurt the design.
- [LCSC](https://www.lcsc.com/) — same warehouse, use the `C…` part number from here.
  Every part in the schematic carries its LCSC number in the `LCSC Part` field.

---

## Library management

Relative library folder: `/lib/`

Every LCSC part gets its own small library, prefixed with `_` so it sorts above the
KiCad standard libraries and is obviously project-specific.

### Download files

Uses [easyeda2kicad](https://github.com/uPesy/easyeda2kicad.py) to pull symbol,
footprint and 3D model from EasyEDA / LCSC.

- `--output`: defines path and name for that library, e.g. `_poti_pta3043`
  - Same name merges multiple parts into one library, e.g. `C:\...\lib\lcsc` puts all parts into a single `lcsc` lib.
  - `--output` must come **before** `--project-relative`
- `--lcsc_id=`: the `C…` ID from the LCSC web page
- Generates 2 folders and 1 file: `_poti_pta3043.3dshapes/`, `_poti_pta3043.pretty/`, `_poti_pta3043.kicad_sym`

```powershell
pip install easyeda2kicad

$id = "C5357832"
$path = "C:\<project_root_folder>\lib\_poti_pta3043"
easyeda2kicad --full --lcsc_id=$id --output $path --project-relative
```

### Import symbol library in KiCad

```
Schematic Editor: Preferences → Manage Symbol Libraries → Tab "Project Specific Libraries" → "+"

- Nickname:       _poti_pta3043
- Library Path:   ${KIPRJMOD}/lib/_poti_pta3043.kicad_sym
- Library Format: KiCad
- Description:    Sliding Poti / 10kΩ / 30mm long / BOURNS PTA3043-2010CIB103
```

### Import footprint library in KiCad

```
PCB Editor: Preferences → Manage Footprint Libraries → Tab "Project Specific Libraries" → "+"

- Nickname:       _poti_pta3043   (must match the symbol library nickname exactly)
- Library Path:   ${KIPRJMOD}/lib/_poti_pta3043.pretty
- Library Format: KiCad
- Description:    Sliding Poti / 10kΩ / 30mm / BOURNS PTA3043-2010CIB103
```

Both tables live in the project root as `sym-lib-table` and `fp-lib-table`, so they
travel with the repository.

### Optionally: clean up in the Symbol Editor

Open Symbol Editor → select the symbol → press `E` with nothing selected →

- Reference: `RV` (see reference designators below)
- Description: Sliding Poti / 10kΩ / 30mm / BOURNS PTA3043-2010CIB103
- **Pin Table**: fix the electrical type of each pin so ERC does not complain

Also check the fields `Manufacturer`, `MPN` (manufacturer part number), `LCSC Part`
and `Datasheet`. Datasheets are linked by URL in this field rather than stored in the
repository.

**Symbol does not show up**: close and reopen KiCad.

### Reference designators (IEEE 315)

| Ref   | Meaning                                              |
|-------|------------------------------------------------------|
| `R`   | Resistor                                             |
| `RV`  | Potentiometer / variable resistor                    |
| `RN`  | Resistor network                                     |
| `C`   | Capacitor                                            |
| `L`   | Inductor                                             |
| `D`   | Diode, LED (also addressable LEDs like WS2812)       |
| `Q`   | Transistor (BJT, MOSFET)                             |
| `U`   | Integrated circuit, module (also DS18B20, ESP32)     |
| `J`   | Connector, header, terminal block, socket            |
| `P`   | Plug (rarely used, KiCad mostly uses `J`)            |
| `JP`  | Jumper                                               |
| `SW`  | Switch, push button                                  |
| `F`   | Fuse                                                 |
| `FB`  | Ferrite bead                                         |
| `Y`   | Crystal, oscillator                                  |
| `T`   | Transformer                                          |
| `K`   | Relay                                                |
| `BT`  | Battery                                              |
| `TP`  | Test point                                           |
| `H`   | Mounting hole                                        |
| `FID` | Fiducial (assembly alignment mark)                   |
| `M`   | Motor, fan                                           |
