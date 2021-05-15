
Add kmer counts

```python
## ...load or create workspace...

## If there is not kc available, create kmer counter and add a count
K=31
kc = ws.add_kmer_counter('pek31', K)
kc.add_count('pe', peds)

## If the kmer counter is already in the ws, get it using the name
print(ws.ls())
kc=ws.get_kmer_counter('pek31')


## Counts are available via nodeviews
nv=ws.sdg.get_nodeview(10)
nv.kmer_coverage("pek31", "pe")

## ...persist as necesary...
```


Compute node mean kmer coverage

Starting form a dataset with a kmer counter already loaded

```python

## ...load or create workspace...

## Check kmer counter and kmer collections are present and take note of the names
print(ws.ls())

## Create a nodeview
nv=ws.sdg.get_nodeview(10)

## Compute kmer coverage mean
print(sum(nv.kmer_coverage('pek31', 'pe'))/nv.size())

```
