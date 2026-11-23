# mapScope manual

This manual covers what you need to work with mapScope. It is deliberately short — most things explain themselves once a map is loaded.

- [Preparing your project](#preparing-your-project)
- [Opening a map](#opening-a-map)
- [The main window](#the-main-window)
- [The structure tree](#the-structure-tree)
- [Pages](#pages)
- [The hex dump](#the-hex-dump)
- [Searching](#searching)
- [Resources](#resources)
- [Reports](#reports)
- [Comparing two builds](#comparing-two-builds)
- [Hex dump in its own window](#hex-dump-in-its-own-window)
- [Printing](#printing)
- [Keyboard shortcuts](#keyboard-shortcuts)
- [Limits](#limits)

## Preparing your project

mapScope needs the **detailed** map file:

**Project → Options → Building → Delphi Compiler → Linking → Map file = Detailed**

Build the project. The linker writes `YourProject.map` next to `YourProject.exe`. mapScope works with the map alone, but keep the executable next to it: then you also get the hex dump, resource previews and string tables.

## Opening a map

- **File → Open** (Ctrl+O) and pick a `.map` file, or
- drag a `.map` file onto the window — dragging the EXE, DLL or BPL works too, mapScope then takes the `.map` next to it, or
- pass the `.map` on the command line: `mapScope.exe C:\Work\MyApp.map`.

Large maps are read in the background; the window stays usable meanwhile. Recently opened files are listed in the **File** menu.

If the map and the executable do not belong together (for example an old map next to a new EXE), the footer shows a warning. Rebuild, then reload with **F5**.

## The main window

<!-- Screenshot: Hauptfenster, Bereiche beschriftet -->
![Main window](img/main-window-parts.png)

- **Left:** the structure tree.
- **Right, top:** the page of the selected item.
- **Right, bottom:** the hex dump of the executable.
- **Footer:** file names, the byte under the mouse, number of units and symbols.

The **View** menu switches the layout (Ctrl+Shift+1/2/3: document and hex dump, document only, large hex dump). Ctrl+Shift+Space maximizes the active pane.

Every selection is remembered: **Alt+←** and **Alt+→** go back and forth; right-click the back button for the history list. Links on the pages work the same way.

Light and dark mode: the moon icon in the bar on the left.

## The structure tree

| Branch | Contents |
|---|---|
| **Segments** | `.text`, `.data`, … — each with the units that contribute to it, down to the single symbols |
| **Units** | every unit with its classes, methods and other symbols |
| **Resources** | resources of the executable by type (icons, strings, forms, …) |
| **Reports** | evaluations across all units (see [Reports](#reports)) |

Each entry shows its size. **Ctrl+F3** (or the magnifier above the tree) filters the tree by name.

**Ctrl+Q** filters only the level of the focused entry (quick filter). The text matches from the beginning of the name; separate parts with spaces, and they must appear in that order — `sys cla` finds `System.Classes`. Start with a space to match anywhere in the name. Pressing **Ctrl+Q** again switches to the other mode, where the letters only have to appear in order (`syscls`).

**Views** — the star above the tree offers different ways to look at the tree, for example *Units by size*, *Units by namespace*, *Large items only (64 KB and more)* or *Without symbols*. The same menu lets you group units and choose the sort order. The chosen view stays when you reload or open another map.

Right-click an entry for its context menu: copy a link to it, or **Show in hex dump**.

## Pages

Selecting an entry shows its page: sizes, charts, and lists that link to related items — a unit page lists its contributions and largest symbols, a symbol page its neighbours, and so on. After loading, the overview page summarizes the whole map.

- **Ctrl+F** searches in the page, **F3** finds the next match.
- **Ctrl+Plus / Ctrl+Minus / Ctrl+0** (or Ctrl+mouse wheel) zoom.
- Tables have a copy button in their corner; the copy goes to the clipboard as text and as a table you can paste into Excel or Word.

## The hex dump

The hex dump shows the bytes of the executable. When you select something in the tree, its bytes are highlighted and scrolled into view. A symbol is shown together with its neighbours; a class or a generic type shows its methods in alternating colours.

- **Click** a byte: the footer tells you the section, the address and the symbol it belongs to.
- **Double-click** a byte: jumps to that symbol or resource in the tree.
- **Drag** (or Shift+click) selects bytes.
- **Right-click** a highlighted area for: *Copy as hex text*, *Copy as text*, *Save bytes…*, *Set length…*, and *Keep mark* — a kept mark stays until you remove it and can be renamed.

## Searching

**Ctrl+F** in the hex dump (or the magnifier in its header) opens the search bar. **F3** / **Shift+F3** go to the next / previous match.

| You type | mapScope searches for |
|---|---|
| `TForm` | the text — as UTF-16, UTF-8 and ANSI at the same time |
| `"Access violation"` | text with spaces: put it in quotes |
| `hex:4D5A` or `hex:"4D 5A 90 00"` | exactly these bytes, in the order you write them |
| `0x00401000` | this number as it is stored in memory (little endian) — e.g. an address from the map or the debugger |
| `1024` | this whole number as stored in memory |

Text is found regardless of case. The **Sel** button limits the search to the bytes of the item selected in the tree.

## Resources

Under **Resources** in the tree you find the resources of the executable by type. Images (PNG, JPEG, GIF, BMP) and icons are shown as a preview — an icon group shows all its sizes side by side. String tables list their texts with their numbers.

## Reports

| Report | What it shows |
|---|---|
| **Generics** | how much of your code are generic instantiations (`TList<T>`, `TDictionary<K,V>` …), which types cost the most, and where the copies come from |
| **Type information** | the size of VMTs and RTTI per unit and namespace |
| **Comparison** | the differences to a baseline (see below) |

## Comparing two builds

To see what a change did to your binary:

1. Load the map of the current build.
2. **File → Keep as baseline**.
3. Change your code and rebuild.
4. Press **F5** (Reload). mapScope opens the comparison: overall numbers, segments, and units that are new, gone or changed in size, plus the changes in generics.

**File → Compare with…** (Ctrl+Shift+O) compares the loaded map with any other `.map` file instead.

## Hex dump in its own window

**View → Hex dump in own window** opens the executable in a separate window, with the bytes of the current selection highlighted.

<!-- Screenshot: Hex-Fenster mit Baum der Markierungen -->
![Hex window](img/hex-window.png)

- The tree on the left lists your kept marks, the bytes of the selected item and the search results. Click an entry to jump there; right-click to rename, remove or keep it (F2 and Del work too).
- **View → Go to offset…** (Ctrl+G) jumps to an offset (decimal, `0x…` or `$…`).
- **View → 8 / 16 / 32 bytes per line**, or **Other width…** for any width — handy for data made of fixed-size records. The footer then shows the record number.
- **File → Save all bytes…** (Ctrl+S).

## Printing

**File → Print preview** (Ctrl+F2), **Print** (Ctrl+P) and **Export as PDF** print the current page.

## Keyboard shortcuts

| Keys | Action |
|---|---|
| Ctrl+O | Open a map |
| F5 | Reload |
| Ctrl+Shift+O | Compare with another map |
| Alt+← / Alt+→ | Back / forward |
| Ctrl+F3 | Filter the tree |
| Ctrl+Q | Filter the current tree level (again: switch mode) |
| Ctrl+F, F3, Shift+F3 | Search in the page or hex dump, next / previous match |
| Ctrl+Plus, Ctrl+Minus, Ctrl+0 | Zoom |
| Ctrl+Shift+1 / 2 / 3 | Layout: document and hex dump / document only / large hex dump |
| Ctrl+Shift+Space | Maximize the active pane |
| F6 / Shift+F6 | Next / previous pane |
| Ctrl+P, Ctrl+F2 | Print, print preview |
| Ctrl+G | Go to offset (hex window) |

## Limits

- A `.map` file contains no type information: the size of a record or class instance cannot be shown.
- Unit contributions per segment are exact. Symbol sizes are an approximation: the distance to the next symbol, capped at the end of the unit's contribution.
- Type information of records and enums has no symbol of its own, so the *Type information* report is an estimate, not an exact sum.
