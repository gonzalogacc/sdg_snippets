
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

Use tangles to evaluate and solve common structures in the graph (WARNING: this operation modifies the graph)

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


Bubble support exploration (WARNING: this operation modifies the graph)

Detect nodes with parallel nodes (bubbles) and count the read support for each side. Delete node with less support (example only)

```python
## ...load or create workspace...
print("Nodes: %s" %len(ws.sdg.get_all_nodeviews()))
ge=SDG.GraphEditor(ws)
for nv in ws.sdg.get_all_nodeviews():
    if len(nv.parallels())==1:
        if len(peds.mapper.get_paths_in_node(nv.node_id())) > len(peds.mapper.get_paths_in_node(nv.parallels()[0].node_id())):
            ge.queue_node_deletion(nv.parallels()[0].node_id())
        else:
            ge.queue_node_deletion(nv.node_id())

ge.apply_all()
ws.sdg.join_all_unitigs()
print("Nodes: %s" %len(ws.sdg.get_all_nodeviews()))

## ...persist as necesary...                                     
```

Solve bubbles using kmer coverage. Checks for unsupported kmers in parallel nodes.

```python

## ...same ws load and pe mapping as before ...

## Check for pe kmer count
print(ws.ls())
KMER_COLLECTION_NAME="main" ## <-- fill from your ws
PE_COUNT_NAME="pe" ## <-- fill from your ws
BUBBLE_SIZE=125 ## <-- Usually 2*K-1 to capture just one erroneous kmer in the middle un a unitig
LOW_CVG=40 ## <-- Frequency below wich a kmer is cosidered an error, extracted from the kmer spectra (at k=PE_COLLECTION kmer size)

## List nodes to delete
to_delete=[]
for nv in ws.sdg.get_all_nodeviews():
    if nv.size()<=BUBBLE_SIZE and len(nv.parallels())==1:
        on=nv.parallels()[0]
        if on.size()<=BUBBLE_SIZE and abs(on.node_id())>nv.node_id():
            nvlc=len([x for x in nv.kmer_coverage(KMER_COLLECTION_NAME,PE_COUNT_NAME) if x<=LOW_CVG])
            onlc=len([x for x in on.kmer_coverage(KMER_COLLECTION_NAME,PE_COUNT_NAME) if x<=LOW_CVG])
            if nvlc and not onlc:
                print('del',nv,nvlc,on,onlc)
                to_delete.append(nv.node_id())
            if onlc and not nvlc:
                print('del',on,onlc,nv,nvlc)
                to_delete.append(abs(on.node_id()))

## Delete nodes
for x in to_delete:
    ws.sdg.remove_node(x)

## Join remaining nodes into unitigs
ws.sdg.join_all_unitigs()

## ...persist as necesary...
```


Clip tipping (WARNING: this operation modifies the graph)

```python
## ...load or create workspace...

## Tip clipping using topology and size only
MAX_TIP_SIZE=125
CLIPPING_ROUNDS=5
gc=SDG.GraphContigger(ws)
gc.clip_tips(MAX_TIP_SIZE, CLIPPING_ROUNDS)

## ...persist as necesary...    
```
