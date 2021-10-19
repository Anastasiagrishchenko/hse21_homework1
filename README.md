# hse21_homework1

mkdir hm1
cd hm1

ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq

seqtk sample -s602 oil_R1.fastq 5000000 > sub1.fq
seqtk sample -s602 oil_R2.fastq 5000000 > sub2.fq
seqtk sample -s602 oilMP_S4_L001_R1_001.fastaq 1500000 > sub_MP1.fq
seqtk sample -s602 oilMP_S4_L001_R2_001.fastaq 1500000 > sub_MP2.fq

mkdir fastqc
ls sub* | xargs -tI{} fastqc -o fastqc {}
mkdir multiqc
multiqc -o multiqc fastqc

platanus_trim sub1.fastq sub2.fastq
platanus_internal_trim sub_MP1.fq sub_MP2.fq

platanus assemble -f sub1.fastq.trimmed sub2.fastq.trimmed 2> assemble.log
grep '^>' out_contig.fa | wc -l ##количество контигов 662
platanus scaffold -c out_contig.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 sub_MP1.fq.int_trimmed sub_MP2.fq.int_trimmed 2> scaffold.log
grep '^>' out_scaffold.fa |wc -l ##количество скаффолдов 75
platanus gap_close -c out_scaffold.fa -IP1 sub1.fastq.trimmed sub2.fastq.trimmed -OP2 sub_MP1.fq.int_trimmed sub_MP2.fq.int_trimmed 2> gapclose.log
echo scaffold1_cov232 > _tmp.txt
seqtk subseq out_gapClosed.fa _tmp.txt > oil_genome.fna
#Скрины общей статистики
![generalstatistic1](https://user-images.githubusercontent.com/91248425/137950277-1db5020e-9f1b-4e51-866e-21279044f24d.jpg)
![fastqccounts](https://user-images.githubusercontent.com/91248425/137950306-d1aff49f-0bfb-4c3a-a65d-1583a0d3818d.jpg)
![meanquality1](https://user-images.githubusercontent.com/91248425/137950329-b1a94435-ea87-4d61-8553-4b1d8307f9d1.jpg)
#скрины статисктики с подрезанными праймерами
![generalstatistics2](https://user-images.githubusercontent.com/91248425/137950483-23851441-0c07-4647-89c3-829fe521b3ee.jpg)
![fastqccounts2](https://user-images.githubusercontent.com/91248425/137950504-1ceaa65a-f006-4dac-81e6-df340d60b96a.jpg)
![meanquality2](https://user-images.githubusercontent.com/91248425/137950531-bc9d55b4-a5b1-4736-9263-804beddfac58.jpg)

