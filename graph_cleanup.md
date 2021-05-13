
Simplest approach to tip clipping and bubble popping.

```python
import SDGPython as SDG

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

Use tangles to evaluate and solve the graph

```python
## ...same ws load and pe mapping as before ...

```
