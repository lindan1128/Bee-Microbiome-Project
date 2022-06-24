# Bee-Microbiome-Project

## Step 1: Remove host reads
  
    	  bwa index apis_cerana.fa
	  ls *_1.fq| awk -F'_' '{print "bwa  mem -t 150 -R \"@RG\\tID:IDa\\tPU:81MMNABXX\\tSM:exomeSM\\tPL:Illumina\" -T 30 apis_cerana.fa "$1"_1.fq "$1"_2.fq > "$1"_bee.sam"}'  |sh
	  ls *bee.sam | awk -F'_' '{print "perl /home/lijun/bin3/sam_identity.pl "$0" > "$1".identity "}' | sh
	  ls *identity | awk -F'.' '{print "awk \x27$2>0.95 {print $1}\x27 "$0" > "$0".0.95"}' | sh
	  ls *_1.fq | awk -F'_' '{print "perl fiter_seq_from_fq_according_2_list.pl "$0" "$1".identity.0.95 > "$1".no-bee_1.fq"}' | sh
	  ls *_2.fq | awk -F'_' '{print "perl fiter_seq_from_fq_according_2_list.pl "$0" "$1".identity.0.95 > "$1".no-bee_2.fq"}' | sh
    
## Step 2: Quality control

    	  ls *.no-bee_2.fq | awk -F'.' '{print "fqc.pl all -p -f "$1".no-bee_1.fq -r "$1".no-bee_2.fq -o "$1".nobee_qc"}' | sh
	  ls *qc_1.fastq|awk -F'_qc_1' '{print "fq2fa --merge "$0" "$1"_qc_2.fastq "$1".qc.fa"}'|sh
    
## Step3: Run kraken2

    	  ls *.nobee.qc.fa |awk -F '.' ' {print "kraken2 --db KRAKEN2_Custom --threads 100 --report kraken2_"$1"_report "$0""} ' | sh
    	  python kraken-multiple-taxa.py -d  kraken2 -r G -c 2 -o kraken-report-genus
	  python kraken-multiple-taxa.py -d  kraken2 -r F -c 2 -o kraken-report-family
	  python kraken-multiple-taxa.py -d  kraken2 -r K -c 2 -o kraken-report-kingdom
	  python kraken-multiple-taxa.py -d  kraken2 -r D -c 2 -o kraken-report-domain
	  python kraken-multiple-taxa.py -d  kraken2 -r U -c 3 -o kraken-report-u
