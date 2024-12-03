# Форматы биоинф файлов

- [fasta](https://en.wikipedia.org/wiki/FASTA_format), fa - первая строка: заголовок начинающийся с `>` и дополнительная информация, вторая и последующие строки: контиг - нуклеотиды на произвольном числе строк (обычно выравненные по 80 символов на строку). Может быть несколько последовательностей в одном файле, каждая последовательность начинается с `>`.
- [fastq](https://en.wikipedia.org/wiki/FASTQ_format) - fasta с качеством - 4 строки
- [gff](https://en.wikipedia.org/wiki/General_feature_format) - general feature format - описания генов, мета-информация к геному (fasta) - 9 столбцов
- [BED](https://en.wikipedia.org/wiki/BED_(file_format))
- BAM - 
- SAM -

## fasta, fa
```
>MCHU - Calmodulin - Human, rabbit, bovine, rat, and chicken
MADQLTEEQIAEFKEAFSLFDKDGDGTITTKELGTVMRSLGQNPTEAELQDMINEVDADGNGTIDFPEFLTMMARKMKDTDSEEEIREAFRVFDKDGNGYISAAELRHVMTNLGEKLTDEEVDEMIREADIDGDGQVNYEEFVQMMTAK*
```

## fastq
```
@SEQ_ID
GATTTGGGGTTCAAAGCAGTATCGATCAAATAGTAAATCCATTTGTTCAACTCACAGTTT
+
!''*((((***+))%%%++)(%%%%).1***-+*''))**55CCF>>>>>>CCCCCCC65
```

## gff
https://ftp.flybase.net/genomes/dmel/current/gff/

```
gzcat dmel-all-r6.60.gff.gz | head
```

Комментарии в начале файла:
```
dmel-all-r6.60.gff0000644000156100001066177140130114701613537014007 0ustar  argosadmfbadmin##gff-version 3
##species http://www.ncbi.nlm.nih.gov/Taxonomy/Browser/wwwtax.cgi?id=7227
##feature-ontology ftp://ftp.flybase.org/releases/FB2024_05/precomputed_files/ontologies/so.obo.gz
##genome-build FlyBase r6.60
##sequence-region 211000022278346 1 1900              # короткие последовательности,
##sequence-region 211000022280240 1 1085              # которые не отностяся к какой-либо хромосоме
##sequence-region 211000022280181 1 1378
##sequence-region 211000022278732 1 2618
...
##sequence-region X 1 23542272                         # стандартная хромосома
##sequence-region XY_mapped_Scaffold_7_D1574 1 50626
##sequence-region Unmapped_Scaffold_32_D1773 1 16158
##sequence-region 4 1 1348132
##sequence-region X3X4_mapped_Scaffold_6_D1712 1 33321
...
```

Сам файл (колонки выравнены руками для читаемости):

```
gzcat dmel-all-r6.60.gff.gz | grep -a -v '^##' | head
```

```
#1  хромосома (2L, 2R, 3L, 3R у дрозофилы)
    или идентификатор контига (если не смогли отнести к какой-либо хромосоме)
#2  источник или утилита, откуда взяты данные или которые нашли данные
#3  тип участка, специфично для утилиты, не всегда понятно что это
#4-5 координаты - с i по j нуклеотид в этом участке находится что-то
#6  не стандартизовано
#7  + или -, цепь ДНК
#8  не стандартизовано
#9  комментарии ключ-значение: e.g. идентификаторы, ссылки на базы данных, parent для вложенных фич

#1 хромосома     #2 источник/утилита         #3 тип участка  #4 #5    #6   #7 #8 #9
211000022278031  FlyBase                     golden_path     1  1021  .	   .  .  ID=211000022278031;Name=211000022278031;Dbxref=GB:DS485919,GB:DS485919,REFSEQ:NW_001847208
211000022278031  DHGP                        repeat_region   1  336   .	   +  .  ID=RR42374_repeat_region;Name=RR42374;location=211000022278031:1..336:+
211000022278031  Tandem_Repeat_Finder_75-20  match           1  336   .	   +  .  ID=:102089_Tandem_Repeat_Finder_75-20_dummy;Name=Tandem_Repeat_Finder_75-20-0-dummy-AABU01000001-336-1;program=Tandem_Repeat_Finder_75-20;programversion=1.0;sourcename=dummy
211000022278031  Tandem_Repeat_Finder_75-20  match_part      1  336   546  +  .  Name=:69145;Parent=:102089_Tandem_Repeat_Finder_75-20_dummy
...
```

## BED
Хромосома, начало, конец

```
# chrom chromStart   chromEnd
chr7    127471196    127472363
chr7    127472363    127473530
chr7    127473530    127474697
```

## Упражнение: из gff в BED
(У меня мак. Если что-то не работает, мб не хватает `brew install coreutils`.)

Полезное:
```
# -s пропустить строку если нет нужной колонки
cut -f 3 -s
# команда awk в одиночных кавычках, чтобы не случилась подстановка $3
gzcat dmel-all-r6.60.gff.gz | grep -a -v '^##' | awk '$3=="gene"' | head
gzcat dmel-all-r6.60.gff.gz | grep -a -v '^##' | awk '$3 ~ "gene|mRNA"' | head
# Чтобы awk не пережевал табы в пробелы: (вроде и так не жуёт, но пусть будет)
gzcat dmel-all-r6.60.gff.gz | grep -a -v '^##' | awk '{FS="\t";OFS="\t"} $3 ~ "gene|mRNA"' | head
```

