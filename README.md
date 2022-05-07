# nanopore_analysis
'''bash
#!/bin/bash

#都使用gzip运行效率很低

echo ">primer1" >adapter_seqs.fa
echo "$primer1" >>adapter_seqs.fa
echo ">primer2" >>adapter_seqs.fa
echo "$primer2" >>adapter_seqs.fa

lastdb adapter_seqs.fa adapter_seqs.fa

lastal -Q 1 -P $t  adapter_seqs.fa <(pv $id) | maf-convert -n tab | cut -f 2,7,10 | uniq | \
gzip > $id.txt.gz

fastx-fetch.pl -i <(zgrep -E '^primer' $id.txt.gz| \
awk '{if($3 == "+"){print $2}}'|sort|uniq) <(zcat $id|pv) | \
gzip > $id.fwd.fq.gz

fastx-fetch.pl -i <(zgrep -E '^primer' $id.txt.gz| \
awk '{if($3 == "-"){print $2}}'|sort|uniq) <(zcat $id|pv) | \
fastx-rc.pl | gzip > $id.rev.fq.gz

pv $id.fwd.fq.gz $id.rev.fq.gz |zcat | gzip > $id.dirAdjusted.fq.gz

porechop -i $id.dirAdjusted.fq.gz --threads $t -o $id.dirAdjusted.chop.fq.gz
#minimap2 -ax splice -uf -t 40 /home/guanguiwen/data/nf-core/references/Homo_sapiens/Ensembl/GRCh37/Sequence/WholeGenomeFasta/genome.fasta

minimap2 -ax splice -uf -k14 -t $t $ref $id.dirAdjusted.fq.gz>$id.sam

samtools view -h -F 4 $id.sam>$id.HBV.sam

samtools view -bS $id.HBV.sam >$id.HBV.bam

samtools sort -l 9 -m 1G -o $id.HBV.sort.bam -T sorted -@ $t $id.HBV.bam

samtools index $id.sort.HBV.bam
'''
