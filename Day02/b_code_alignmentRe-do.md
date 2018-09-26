In order to improve the alignment results for the yeast samples, we re-run the
alignment for several samples.
The most important parameters that we tuned were related to the intron sizes.

To determine suitable numbers for  `IntronMin` and `IntronMax` parameters,
we downloaded a bed file for the yeast introns from UCSC table browser (details [at biostars](https://www.biostars.org/p/13290/))

```
# get min. intron size
awk '{print $3-$2}' introns_yeast.bed | sort -k1n | uniq | head -n 3
1
31
35

# get max. intron size
awk '{print $3-$2}' introns_yeast.bed | sort -k1n | uniq | tail -n 3
1623
2448
2483
```

Now that we have a feeling for what the sizes of annotated introns look like,
we can re-run `STAR`.
Note that we used the materials from a previous class since we did not re-run it on the amazon cloud, but our own servers, where we had also downloaded more fastq files than just those for `WT_1` and `SNF2_1`

```
runSTAR=~/mat/software/STAR-2.5.3a/bin/Linux_x86_64/STAR
REF_DIR=~/mat/referenceGenomes/S_cerevisiae/STARindex/

for SAMPLE in WT_1 WT_2 WT_3 WT_4 WT_5 SNF2_1 SNF2_2 SNF2_3 SNF2_4 SNF2_5
do
	FILES=`ls -m /zenodotus/abc/store/courses/2017_rnaseq/rawReads_yeast_Gierlinski/${SAMPLE}/*fastq.gz | sed 's/ //g'`
    FILES=`echo $FILES | sed 's/ //g'`
	echo "Aligning files for ${SAMPLE}, files:"
	echo $FILES 
    $runSTAR --genomeDir ${REF_DIR} --readFilesIn $FILES --readFilesCommand gunzip -c  --outFileNamePrefix ${SAMPLE}_  --outFilterMultimapNmax 1 outSAMtype BAM SortedByCoordinate --runThreadN 2 --twopassMode Basic  --alignIntronMin 1  --alignIntronMax 3000 
done
```