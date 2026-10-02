# der die das — dictionary data

The German dictionary the [der die das](https://github.com/benfangliu/derdiedas) app
reads. Published here as release assets because the full file is 172 MB, well past the
100 MiB ceiling Git enforces on a pushed blob.

Get it from the [latest release](https://github.com/benfangliu/derdiedas/releases).

## What is in the file

`dictionary-en-v2.sqlite` — German headwords with English meanings. Plain SQLite, no
extensions, no FTS5, readable by anything that opens a `.sqlite` file.

| | |
|---|---|
| Lemmas | 89,632 |
| Inflected forms | 843,661 |
| Glosses | 108,586 |
| Size | 172,568,576 bytes |
| MD5 | `687026dacf7b6a878534b045c498a30a` |
| Schema version | 6 |
| Base version | 2 |

Five tables:

- **`lemmas`** — headword, part of speech, gender (`der`/`die`/`das`), plural, and a
  frequency rank where lower is more prominent.
- **`inflections`** — every surface form mapped back to its lemma, folded for lookup, so
  `ging` resolves to `gehen` and `tuer` finds `Tür`. The headword's own row is in here
  too, which is why a lookup of a dictionary form and a lookup of a conjugation take the
  same path.
- **`glosses`** — one row per sense.
- **`gloss_search`** — each significant word of each gloss, with its position in the
  gloss, so an English term finds the German word: `fork` → *Gabel*.
- **`meta`** — schema and base version, source, license, and whether the file is the
  `full` dictionary or a `starter` cut.

Lemma IDs are derived from part of speech plus the folded headword, so they are stable
across rebuilds. A dictionary for another language lines up with this one by ID.

## The starter

The app does not ship this file. It bundles a 32 MB **starter** carved out of it —
every noun that has a gender, one gloss each, and the headword rows of `inflections` —
which is enough to look a word up and learn its article offline from first launch. The
full file is what adds every sense and every inflected form, and it is downloaded on
request rather than bundled.

## manifest.json

The registry the app fetches to find out which dictionaries exist. Each entry carries
the size and MD5 that the download is checked against before it is installed, plus the
base and schema versions that decide whether this build can use it at all.

Pack URLs in it are **relative**, so they resolve against whatever address the manifest
itself was fetched from — the same manifest works from a release page, a CDN or a
laptop on the same network, without being rebuilt for each.

## Rebuilding

From the app repository:

```
npm run download:dictionary   # ~1 GB Wiktextract dump into .cache/
npm run build:dictionary      # full dictionary, starter, and manifest
```

The build is deterministic: the same dump produces a byte-identical file, so the MD5 in
the manifest only changes when the data does.

## License

Dictionary data adapted from [Wiktionary](https://www.wiktionary.org/) via
[Wiktextract](https://kaikki.org/), licensed under
[CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).

These files are an adaptation of that data and are redistributed under the **same
license**. If you redistribute them, or anything built from them, you must credit
Wiktionary and its contributors, link to the license, and release your version under
CC BY-SA 4.0 as well.
