# dataset_v2_main

1008 images, 13 classes: `dataset_v2` with **acral lentiginous melanoma** and **onychogryphosis** removed. Images were copied (not moved); `dataset_v2` is unchanged.
Built by `analysis/scripts/s13_build_dataset_v2_main.py`. `manifest.csv` / `folds.csv` are the v2 rows for the kept classes (same ids, paths, groups and folds).

## Why the two classes were removed
ALM and onychogryphosis were removed because their images come only from the augmented archive5 source. A scraping pilot (Europe PMC, 15 results per term) found no usable nail images for ALM (the 7 figures the project owner reviewed were skin images, not nails) and only 6 usable images for onychogryphosis, too few to add a second source.

## Counts
```
                       0   1   2   3   4  total  B1B2CD  BASE  archive5  archive6  ext_share
class                                                                                       
clubbing              30  32  29  28  31    150       0    39       111         0       0.74
onychomycosis         30  30  29  28  33    150      75     0         0        75       1.00
psoriasis             31  28  31  31  29    150       0    40         0       110       0.73
healthy               32  29  32  26  31    150       0    56        47        47       0.63
splinter haemorrhage  12  13  14  12  11     62       0    62         0         0       0.00
onycholysis            9   8   9  11  13     50       0    50         0         0       0.00
bluish nail            8  11   9  11  10     49       0    49         0         0       0.00
Darier's disease       8  11   8   9   9     45       0    45         0         0       0.00
eczema                 9   7   9  11   7     43       0    43         0         0       0.00
alopecia areata       10   8   7  10   7     42       0    42         0         0       0.00
Beau's lines           7   7   8  10   9     41       0    41         0         0       0.00
koilonychia            7   6   9   9   7     38       0    38         0         0       0.00
half-and-half nails    8   6   7   8   9     38       0    38         0         0       0.00
```
Overall external share 46.1%; max/min class ratio 150/38 = 3.95. Related pairs crossing folds: 0 (asserted; recomputed from original hashes/embeddings).

## Remaining shortcut risks (see `verification_rf.csv`, `diagnostic_shortcuts.csv`)
healthy, clubbing and psoriasis still mix BASE with archive5 / archive6 images whose source remains identifiable. Licences / provenance: as dataset_v1 - none of BASE, archive5, archive6, B1B2CD ships a licence; research/education only, do not redistribute.
