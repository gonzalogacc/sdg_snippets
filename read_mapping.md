pe mapping

## ...load or create workspace...

peds=ws.add_paired_reads_datastore('./sdg_data/paired_end_reads.prseq', 'pe')
MAPPING_K=31
peds.mapper.path_reads(MAPPING_K)

## ...persist as necesary...
