

ROOT=/home/ubuntu/prj/snpexample/
DOVETOOLS=/home/ubuntu/src/dovetail_tools/
LIB_NAME=LM979

snp_pipe:
	${DOVETOOLS}/snps/snps_pipeline.py -B ${ROOT}/${LIB_NAME}.bam \
	-O ${ROOT}/snpoutput \
	-R ${ROOT}/ref/hg38/GRCh38.p12.fa \
	-L ${ROOT}/misc/hg38_intervals.txt \
	-dbsnp ${ROOT}/ref/hg38/dbSNP/00-common_all.vcf.gz \
	-t 1 

hqregion: 
	${DOVETOOLS}/snps/get_HQ_region_bed.py -bam ${ROOT}/bam/${LIB_NAME}.bam -bedroot ${ROOT}/snpoutput/${LIB_NAME}
	bedtools intersect -header -a ${ROOT}/snpoutput/${LIB_NAME}.gz -b ${ROOT}/snpoutput/${LIB_NAME}_highconf.bed > ${ROOT}/snpoutput/${LIB_NAME}_highconf.vcf
	bgzip ${ROOT}/snpoutput/${LIB_NAME}_highconf.vcf
	tabix -p vcf ${ROOT}/snpoutput/${LIB_NAME}_highconf.vcf.gz
	bedtools intersect -a ${ROOT}/truthset/ConfidentRegions.bed.gz -b ${ROOT}/snpoutput/${LIB_NAME}_highconf.bed > ${ROOT}/snpout/ConfidentRegionsAND${LIB_NAME}HQ.bed


concordance:
	gatk Concordance \
	-R ${ROOT}/ref/hg38/GRCh38.p12.fa \
	-isr INTERSECTION \
	-L ${ROOT}/snpout/ConfidentRegionsAND${LIB_NAME}HQ.bed \
	-truth ${ROOT}/truthset/NA12878.vcf.gz \
	-eval ${ROOT}/snpoutput/small_variants/${LIB_NAME}.vcf.gz \
	--summary ${ROOT}/snpoutput/small_variants/evalconfNhq/summary.tsv \
	-tpfp ${ROOT}/snpoutput/small_variants/evalconfNhq/tpfp.vcf \
	-tpfn ${ROOT}/snpoutput/small_variants/evalconfNhq/tpfn.vcf

all: snp_pipe hqregion concordance
