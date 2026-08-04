# kmhelpers_big_data
kmhelpers tutorial on how to deal with large datasets 

How lists were generated with 1O^6 distinct samples:

```bash
for i in 1 2 3 4 5; do
  kmhelpers test create-list -o ./list/data_${i}.jsonl -n 2000000 -s $((12344 + i)) -p data_${i} -mu 500000000 -sd 3
done
```

Lists are not versioned here: the seeds make them reproducible with the command
above. Generated with kmhelpers `<commit>` (branch `big-data`), any change to the
kmer_count distribution invalidates the profiles below.

Sample names in list data_{X}:

```
data_{X}_0000000
...
data_{X}_1999999
```

Samples are not actually created, but are assigned a virtual number of k-mers
drawn from a lognormal distribution, log2(kmer_count) ~ N(log2(median), stddev):
- k=25
- median=500000000 (span 28.9)
- stddev=3 (in spans, not in kmer counts)

```bash
for i in 1 2 3 4 5; do
  kmhelpers profile list/data_${i}.jsonl -o profile/data_${i} -b 1.1 -g 20
done
```

~5s per profile command

Looking to generated files, biggest sample is found in profile/data_4/profile.yaml
```
biggest_sample: ('data_4_0018995', 23529197990892)
max_kmer_count: 25776858722105
```

Compose 

```
kmhelpers compose -o compose -n index list/data_1.jsonl --profiles-file profile/data_4/profile.yaml   
```

46.50s time
~2GB peak RAM
