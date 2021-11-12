# testSAMTOOLS
Test samtools commands

## basic

```bash
samtools view -F 12 example.sam > test.sam

test.sam
    352029 lines

exclude header:
    >samtools view test.sam | wc -l
    310351 line

reads:
    > samtools view test | awk '{print $1}' | sort | uniq | wc -l
    106490 reads
```

## sort

### sort name

```bash
samtools sort -n test.sam -o test.name.bam

SRR12387520.1   99      AT1G76930.2     677     60      46M     =  816  183     TTACTCTCCCCCTACGGTATACCATTCTCCTCCCCCTCCAGTACAC  AFJJJJFJJJJJ<JJJJFFJJAFJJFJFFJ-FJ-AFJJJJJJFFFJ  AS:i:-3 XN:i:0  XM:i:1  XO:i:0  XG:i:0  NM:i:1  MD:Z:33T12      YS:i:-2 YT:Z:CP NH:i:1
SRR12387520.1   147     AT1G76930.2     816     60      2S44M   =  677  -183    CCATACACAAGAAAACAAAGAATCGTTTGGTGATATAATAAAAGAC  --<J<JF<-J<AFAJF--AJJJJJJJJJJ-<<<AJJ-<JJJJFFFA  AS:i:-2 ZS:i:-2 XN:i:0  XM:i:0  XO:i:0  XG:i:0  NM:i:0  MD:Z:44 YS:i:-3 YT:Z:CP NH:i:1
```

### sort position
```
samtools sort test.sam -o test.bam
```

## fixmate

### add mark

**Need name sorted**
``samtools fixmate -m test.name.bam test.fix.bam``
no filter just mark -m
add MQ, MC and ms

```python
SRR12387520.1   99      AT1G76930.2     677     60      46M     =       816     183     TTACTCTCCCCCTACGGTATACCATTCTCCTCCCCCTCCAGTACAC  AFJJJJFJJJJJ<JJJJFFJJAFJJFJFFJ-FJ-AFJJJJJJFFFJ  AS:i:-3 XN:i:0  XM:i:1  XO:i:0  XG:i:0  NM:i:1  MD:Z:33T12      YS:i:-2 YT:Z:CP NH:i:1  MQ:i:60 MC:Z:2S44M ms:i:1418
SRR12387520.1   147     AT1G76930.2     816     60      2S44M   =       677     -183    CCATACACAAGAAAACAAAGAATCGTTTGGTGATATAATAAAAGAC  --<J<JF<-J<AFAJF--AJJJJJJJJJJ-<<<AJJ-<JJJJFFFA  AS:i:-2 ZS:i:-2 XN:i:0  XM:i:0  XO:i:0  XG:i:0  NM:i:0  MD:Z:44 YS:i:-3 YT:Z:CP NH:i:1  MQ:i:60 MC:Z:46M  ms:i:1711
```

### fixmate rm

```bash
samtools fixmate -r -m test.name.bam test.fixAndRM.bam
212980 line

106490 reads
```

## markdup

```bash
#need sorted
samtools sort -o test.fix.sort.bam test.fix.bam
samtools markdup test.fix.sort.bam test.mark.bam

SRR12387520.1   99      AT1G76930.2     677     60      46M     =  816  183     TTACTCTCCCCCTACGGTATACCATTCTCCTCCCCCTCCAGTACAC  AFJJJJFJJJJJ<JJJJFFJJAFJJFJFFJ-FJ-AFJJJJJJFFFJ  AS:i:-3 XN:i:0  XM:i:1  XO:i:0  XG:i:0  NM:i:1  MD:Z:33T12      YS:i:-2 YT:Z:CP NH:i:1  MQ:i:60 MC:Z:2S44M      ms:i:1418
SRR12387520.1   147     AT1G76930.2     816     60      2S44M   =  677  -183    CCATACACAAGAAAACAAAGAATCGTTTGGTGATATAATAAAAGAC  --<J<JF<-J<AFAJF--AJJJJJJJJJJ-<<<AJJ-<JJJJFFFA  AS:i:-2 ZS:i:-2 XN:i:0  XM:i:0  XO:i:0  XG:i:0  NM:i:0  MD:Z:44 YS:i:-3 YT:Z:CP NH:i:1  MQ:i:60 MC:Z:46M        ms:i:1711

#rm
samtools markdup -r test.fix.sort.bam test.mark.bam
275345 line
93047 reads  (rm 1.5k reads from fixmate)

# but SRR12387520.1 did't rm, still dup reads

```

### grep NH and ZS

```bash
samtools view -h test.sam | grep -E '^@|NH:i:1' | grep -v "ZS:i" | samtools sort -o testUniq.sort.bam

134333 lines
reads = 71915



what about fix and mark?
for fix with same command:
    134333 lines
    reads = 71915


for mark
    109983 lines
    58717 reads
```

## what markdup removed and why dup still?

