
```
ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq 
ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq 
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq 
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq 
```
Выбираем рандомные риды:
```
seqtk sample -s301 oil_R1.fastq 5000000 > sub1.fq
seqtk sample -s301 oil_R2.fastq 5000000 > sub2.fq
seqtk sample -s301 oilMP_S4_L001_R1_001.fastq 1500000 > sub1_MP.fq
seqtk sample -s301 oilMP_S4_L001_R2_001.fastq 1500000 > sub2_MP.fq
```
Делаем оценку качества для чтений:
```
mkdir fastqc
ls *.fastq | xargs -P 4 -tI{} fastqc -o fastqc {}

mkdir multiqc
multiqc -o multiqc fastqc
```
Обрезаем:
```
platanus_trim sub1.fq sub2.fq
platanus_internal_trim sub1_MP.fq sub2_MP.fq
```

Повторяем оценку для обрезанных чтений:
```
mkdir trimmed_fastqc
ls sub* | xargs -tI{} fastqc -o trimmed_fastqc {}

mkdir trimmed_multiqc
multiqc -o trimmed_multiqc trimmed_fastqc
```

Склеиваем геном:
```
time platanus assemble -o Poil -t 2 -m 16 -f trimed_fastq/R1_pe.fastq.trimmed trimed_fastq/R2_pe.fastq.trimmed 2> assemble.log
time platanus scaffold -o Poil -t 2 -c Poil_contig.fa -IP1 trimed_fastq/R1_pe.fastq.trimmed trimed_fastq/R2_pe.fastq.trimmed -OP2 trimed_fastq/R1_mp.fastq.int_trimmed trimed_fastq/R2_mp.fastq.int_trimmed 2> scaffold.log
time platanus gap_close -o Poil -t 2 -c Poil_scaffold.fa -IP1 trimed_fastq/R1_pe.fastq.trimmed trimed_fastq/R2_pe.fastq.trimmed -OP2 trimed_fastq/R1_mp.fastq.int_trimmed trimed_fastq/R2_mp.fastq.int_trimmed 2> gapclose.log
```

## Исходные данные
![image](https://user-images.githubusercontent.com/71615626/139140129-ea8a6591-5c01-46b0-8055-0524a86bac06.png)
![image](https://user-images.githubusercontent.com/71615626/139140435-85e24f19-01aa-4c32-86c6-290d95d95171.png)
![image](https://user-images.githubusercontent.com/71615626/139140580-9079ea70-a84a-44a0-b2b8-b91215bd7137.png)
![image](https://user-images.githubusercontent.com/71615626/139140729-ab53bc31-d6a1-4aa4-9312-fc94cec47cd1.png)
## Данные после обрезки
![image](https://user-images.githubusercontent.com/71615626/139140810-dfe2d3e3-f461-44fd-b29a-6ecdffcd3a5a.png)
![image](https://user-images.githubusercontent.com/71615626/139140887-c79d176b-03fe-4d59-a621-b9dc6e244119.png)
![image](https://user-images.githubusercontent.com/71615626/139140931-18a8be74-85b1-4edc-b696-dee8df63613e.png)
![image](https://user-images.githubusercontent.com/71615626/139140973-52d78013-f391-4a52-9214-447788f4a7dd.png)
