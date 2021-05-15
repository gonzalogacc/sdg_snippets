## Path reads

Path reads threads reads across nodes using unique kmer matching


PE

```python
## ...load or create workspace...

peds=ws.add_paired_reads_datastore('./sdg_data/paired_end_reads.prseq', 'pe')
MAPPING_K=31
peds.mapper.path_reads(MAPPING_K)

## Mappings are stored inside the maper (get read paths in node 10)
peds.mapper.get_paths_in_node(10)

## ...persist as necesary...
```


Long reads

```python
## ...load or create workspace...

lords=ws.add_paired_reads_datastore('./sdg_data/long_reads.prseq', 'long_reads')
MAPPING_K=31
lords.mapper.path_reads(MAPPING_K)

## Mappings are stored inside the maper (get read paths in node 10)
lords.mapper.get_paths_in_node(10)

## ...persist as necesary...
```
