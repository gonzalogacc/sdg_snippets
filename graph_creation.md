
DBG graph using python

Simples dbg style graph, no cleanup no nothing

```python

import SDGpython as SDG

## Create workspace and attach a pe datastore (aready created using binaries)
ws=SDG.WorkSpace()
peds=ws.add_paired_reads_datastore('./sdg_data/paired_end_reads.prseq', 'pe')

## Create a graph maker and make a graph from a pe ds
K=31
MIN_COV=2
NUM_BATCHES=16
gm=SDG.GraphMaker(ws.sdg)
gm.new_graph_from_paired_datastore(peds, K, MIN_COV, NUM_BATCHES)

## Print some stats
print(ws.ls())

## Persist the workspace with the graph (optional)
ws.dump('./raw_dbg_graph.sdgws')

## Output the graph as GFA1 (optional)
ws.sdg.write_to_gfa1('./raw_dbg_graph.gfa')

```
