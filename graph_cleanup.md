
Simplest approach to tip clipping and bubble popping.

```python
import SDGpython as SDG

## Load workspace with raw graph
ws=SDG.WorkSpace('./raw_workspace.sdgws')

## Tip clipping using topology and size only
MAX_TIP_SIZE=125
CLIPPING_ROUNDS=5
gc=SDG.GraphContigger(ws)
gc.clip_tips(MAX_TIP_SIZE, CLIPPING_ROUNDS)

## Simple bubble popping using pe support
MAPPING_K=31
peds=ws.add_paired_reads_datastore('./sdg_data/paired_end_reads.prseq', 'pe')
peds.mapper.path_reads(MAPPING_K)

MAX_BUBBLE_SIZE=125
MIN_SUPPORT=6 
MAX_NOISE=5
SNR=10
gc.pop_bubbles(peds, MAX_BUBBLE_SIZE, MIN_SUPPORT, MAX_NOISE, SNR)

## Persist ws or graph (as preferred)
ws.dump('./dbg_tipclip_bubblepop.sdgws')
ws.sdg.write_to_gfa1('./dbg_tipclip_bubblepop.gfa')

```

Solve bubbles using kmer coverage

```python
## ...same ws load and pe mapping as before ...

## ...persist as necesary...
```

Use tangles to evaluate and solve common structures in the graph

```python
## ...same ws load and pe mapping as before ...
from collections import Counter

## Clasify graph structures
FRONTIER_SIZE=100
tangles=ws.sdg.get_all_tangleviews(FRONTIER_SIZE)
print(Counter([tv.classify_tangle() for tv in tangles]).most_common())

gc=SDG.GraphContigger(ws)
ge=SDG.GraphEditor(ws)
strider = SDG.Strider(ws)
strider.add_datastore(peds)

not_solved=solved=0
for t in tangles:
    if t.classify_tangle() == "bubble":
        if gc.solve_bubble(t, strider, ge): solved+=1
        else: not_solved+=1
print("Bubbles solved/not_solved: %s/%s" %(solved, not_solved))

ge.apply_all()
ws.sdg.join_all_unitigs()

## ...persist as necesary...
```
