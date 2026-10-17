# Kiszonki — Brine Calculator

A single-page salt calculator for Polish lacto-fermented vegetables (*kiszonki*). It figures salt against **all** the water in the jar — including the water already inside the vegetables — not just the water you pour in.

**Live:** https://mpioszak.github.io/Kiszonki/

---

## Why it exists

Most pickling recipes give salt as a percentage of the brine you mix, or worse, in tablespoons. Both go wrong:

- **Vegetables are mostly water.** Cucumbers are ~96% water; that water joins the brine and dilutes it. A 3% brine poured over cucumbers finishes well under 3%, which is how jars turn soft and slimy.
- **Salt densities differ by more than double.** A tablespoon is 8.5 g of Diamond Crystal kosher and 18.5 g of pickling salt. Volume measurements aren't portable between boxes.

The calculator works in grams against the total aqueous phase — the liquid the lactic bacteria actually live in — and shows its arithmetic so you can check it.

## What it does

- **Ten Polish ferments** with per-vegetable water content, default salinity, method, typical range, room-temperature stage and holding temperature.
- **Two methods.** Poured brine, or dry salted (kraut-style, salt as a share of vegetable weight).
- **Jar sizing.** Pick a jar or crock size and it estimates how much vegetable packs in and how much water goes around it, using a per-vegetable packing fraction.
- **Derivation panel.** Every figure that goes into the result, including the brine's *pouring* strength versus its *equilibrium* strength, and the top-up brine ratio in g per litre.
- **Salt conversion table.** The target weight rendered as spoons for six common salts — as a fallback, with the caveat attached.
- **Unit toggle** between grams/ml and pounds/cups. Internals stay metric.
- **Range flags.** Warns below 1.5% (loose ferment, soft texture) and above 5% (curing, not fermenting).

## The math

**Poured brine**

```
vegetable water = vegetable weight × water fraction
total water     = vegetable water + water added
salt            = total water × target %
```

The salt all dissolves in the water you pour, so the brine enters the jar considerably stronger than the target and diffuses down to it over two or three days. The panel reports both numbers.

**Dry salted**

```
salt           = vegetable weight × target %
juice salinity = salt ÷ vegetable water
```

Dry salting takes its percentage from the vegetable rather than from water, so the released juice sits above the nominal figure. The panel reports that equilibrium value, which is what makes a kraut recipe comparable to a brined one.

**Jar sizing**

```
vegetable = litres × 1000 × packing fraction
water     = litres × 1000 × (1 − packing fraction) × 0.94   (0 for dry salting)
```

**Spoons** divide the target weight by grams-per-tablespoon for the chosen salt, rounded to a quarter teaspoon and rolled up to cups past 16 tbsp.

### Vegetables

| Key | Ferment | Water | Default | Method |
| --- | --- | --- | --- | --- |
| `ogorki` | Ogórki kiszone — dill cucumbers, whole | 96% | 3.0% | brine |
| `malosolne` | Ogórki małosolne — half-sours | 96% | 2.0% | brine |
| `kapusta` | Kapusta kiszona — sauerkraut, shredded | 92% | 2.0% | dry |
| `kapustamarch` | Kapusta z marchewką — kraut with carrot | 91% | 2.0% | dry |
| `pomidory` | Pomidory zielone kiszone — green tomatoes | 93% | 3.0% | brine |
| `buraki` | Buraki kiszone — beets, for barszcz | 88% | 2.0% | brine |
| `rzepa` | Rzepa / rzodkiew kiszona — turnip or daikon | 90% | 2.5% | brine |
| `papryka` | Papryka kiszona — peppers | 94% | 3.0% | brine |
| `czosnek` | Czosnek kiszony — garlic cloves | 59% | 3.0% | brine |
| `grzyby` | Grzyby kiszone — milk caps, rydze | 92% | 3.0% | dry |

Garlic is the clearest case for the whole approach: at 59% water it contributes far less of its own liquid than a cucumber, so salting by total mass misses badly.

### Salt densities (g per tablespoon)

| Salt | g/tbsp |
| --- | --- |
| Sól kamienna niejodowana — coarse rock | 16 |
| Pickling / canning salt | 18.5 |
| Fine sea salt | 17 |
| Non-iodized table salt | 18 |
| Diamond Crystal kosher | 8.5 |
| Morton kosher | 14.5 |

Non-iodized salt only. Iodine and anti-caking agents hold back the bacteria you're trying to feed.

## Running it

One file, no build step, no package manager:

```
git clone https://github.com/Mpioszak/Kiszonki.git
cd Kiszonki
open index.html          # or: python3 -m http.server
```

Deployed through GitHub Pages from `main`.

### Structure

```
index.html    # markup, styles and logic — the entire application
```

- Vanilla JavaScript, no framework or bundler.
- The only external request is a Google Fonts stylesheet (IBM Plex Sans, IBM Plex Mono, Zilla Slab). System-font fallbacks are declared, so it renders fine offline.
- Light and dark themes via `prefers-color-scheme`, with a `data-theme` attribute override.
- Two-column instrument-panel layout, collapsing to one column below 860px.

### Changing the data

Vegetables live in the `VEG` object near the top of the script. Each entry takes:

| Field | Meaning |
| --- | --- |
| `label` | Name shown in the dropdown |
| `water` | Water content as a fraction of weight |
| `pct` | Default target salinity |
| `method` | `"brine"` or `"dry"` |
| `pack` | Fraction of jar volume the vegetable occupies |
| `days`, `temp`, `range` | Displayed guidance strings |
| `note` | Practical note shown under the ferment panel |

Salts are `[name, grams per tablespoon]` pairs in `SALTS`. Adding to either list needs no other changes.

## Accuracy

Water contents are USDA averages and move with the season and how long the vegetables sat in a cooler. Packing fractions are estimates — weigh what you actually pack. The output is a starting point; taste as you go.

## License

Not yet specified. Add a `LICENSE` file if you want to set terms for reuse.
