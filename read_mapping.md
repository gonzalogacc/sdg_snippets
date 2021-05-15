pe mapping

```python
## ...load or create workspace...

peds=ws.add_paired_reads_datastore('./sdg_data/paired_end_reads.prseq', 'pe')
MAPPING_K=31
peds.mapper.path_reads(MAPPING_K)

## Mappings are stored inside the maper (get read paths in node 10)
peds.mapper.paths_in_node[10]

## ...persist as necesary...
```

