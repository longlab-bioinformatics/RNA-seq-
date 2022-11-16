# RNA-seq
RNA-seq  processing pipelines

#!/bin/bash

#SBATCH --partition=normal

#SBATCH -t 6:00:00

#SBATCH --array=0-5

#SBATCH --mem=10G

#SBATCH --cpus-per-task=8

#SBATCH --job-name=Hisat2_stringtie

#SBATCH --nodes=1

libraries=(B73_anther_rep1_SRR650382 B73_meiocyte_rep1_SRR650383 B73_seedling_rep1_SRR650381 B73_anther_rep2_SRR650379 B73_meiocyte_rep2_SRR650380 B73_seedling_rep2_SRR650378)

hisat2 -p 8 --dta -x ~/JinchengLong/Index/Hisat2_index/Zm.genome_tran -U ../../../Clean_Reads/RNA_seq/${libraries[$SLURM_ARRAY_TASK_ID]}_trimmed.fq.gz -S ./${libraries[$SLURM_ARRAY_TASK_ID]}.sam

~/JinchengLong/SoftWare/samtools-1.16.1/samtools sort -@ 8 -o ./${libraries[$SLURM_ARRAY_TASK_ID]}_sorted.bam ./${libraries[$SLURM_ARRAY_TASK_ID]}.sam

~/JinchengLong/SoftWare/samtools-1.16.1/samtools index -@ 2 ./${libraries[$SLURM_ARRAY_TASK_ID]}_sorted.bam

stringtie -p 8 -G ~/JinchengLong/Maize_annotation_files/Zea_mays.Zm-B73-REFERENCE-NAM-5.0.54.gtf -o ${libraries[$SLURM_ARRAY_TASK_ID]}.gtf -A ${libraries[$SLURM_ARRAY_TASK_ID]}.tab -B -e -l ${libraries[$SLURM_ARRAY_TASK_ID]} ${libraries[$SLURM_ARRAY_TASK_ID]}_sorted.bam
