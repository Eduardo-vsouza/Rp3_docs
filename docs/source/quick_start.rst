Quick Start
=====

.. _quick_start:

Quick Start
------------


1. I already have an assembled transcriptome
----------------

If you already have a transcriptome, start the pipeline on the ``database`` mode and provide the path to the folder containing your GTF files with the ``--gtf_folder`` parameter (skip to step 3)

2.  ###### I have RNA-Seq reads, but still need to assemble a transcriptome
You should assemble your transcriptome using a transcriptome assembler such as StringTie or Cufflinks after aligning the reads to the genome with a splice-aware aligner such as STAR or hisat2. Rp3 needs the GTF file produced by these assemblers.

###### Using the reference GTF
Alternatively, you can just use a reference GTF file from Ensembl or NCBI, for instance. The GTF files has to contain 'transcript' and 'exon' features. The limitation when using a reference GTF is that you cannot include novel transcripts for your samples. The pipeline will then perform the 3-frame translation on already annotated transcripts. Annotated ORFs will be removed afterwards.

3. ###### Generate a custom proteomics database
Run RP3 on the ``database`` mode
.. code-block:: console
   $ rp3.py database --outdir <path/to/output/directory> --threads 8 --genome <path/to/genome.fasta> --gtf_folder <path/to/gtf/folder> --proteome <path/to/reference_proteome.fasta

- The specified ``--gtf_folder`` should be the folder containing the GTF file with the transcriptome assembly. If you already have a GTF file, provide the folder containing it and other GTF files to be included in the analysis. **Provide the path to a GTF file.**

4. ###### Perform the peptide search
Run RP3 on the ``search`` mode.
``$ rp3.py search --mzml /path/to/mass/spec/folder --outdir path/to/output/directory/ --threads 8``
	- The ``--mzml`` expects a folder containing subdirectories corresponding to each group (see Peptide search section)

5. ###### Re-score your results
Run the pipeline on ``rescore`` mode.
``$ rp3.py rescore --outdir /path/to/output/directory --threads 8 --mzml /path/to/mzmz/files --proteome /path/to/reference/proteome --msPattern mzML``


6. ###### Check if the proteogenomics smORFs have Ribo-Seq coverage
Run the pipeline on ``ribocov`` mode.
``$ rp3.py ribocov --outdir /path/to/output/directory --threads 8 --fastq /path/to/fastq/folder --gtf /path/to/gtf/file --genome_index /path/to/genome/index --cont_index /path/to/contaminants/index --plots``

###### Notes
- Always use the **same output directory** when running different modes for the same analysis.

