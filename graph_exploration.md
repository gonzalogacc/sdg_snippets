
Size and linkage

```python
## ...load or create workspace...

## General workspace information
print(ws.ls())

## Get number of active nodes in the graph
print(len(ws.sdg.get_all_nodeviews()))

```

Length stats by kci

```python

## Length stats by kci
## Set the distribution peak
kc=ws.get_kmer_counter('pek31')

for i, c in enumerate(kc.count_spectra('pe')):
    print("%s,%s" %(i, c))
    
kc.set_kci_peak(46)    
kc.update_graph_counts()
kc.compute_all_kcis()
print(ws.sdg.stats_by_kci())

##  -----------------------------------------------------------------------------------
## |  KCI  |    Total bp   |   Nodes  |  Tips   |     N25    |     N50    |     N75    |
## |-------+---------------+----------+---------+------------+------------+------------|
## | None  |        142280 |     4135 |      33 |         37 |         34 |         32 |
## | < 0.5 |        802501 |    13119 |      23 |         61 |         61 |         61 |
## | ~ 1   |       5153554 |    22910 |       2 |        980 |        546 |        233 |
## | ~ 2   |         77149 |      632 |       0 |        631 |        226 |         61 |
## | ~ 3   |         10869 |      191 |       0 |         61 |         53 |         46 |
## | > 3.5 |         15930 |      320 |       0 |         54 |         47 |         43 |
## |-------+---------------+----------+---------+------------+------------+------------|
## | All   |       6202283 |    41307 |      58 |        876 |        425 |         61 |
##  -----------------------------------------------------------------------------------
 
```
