Installation
=====

.. _installation:

System requirements
----------------

The pipeline was tested on Ubuntu 22.04 LTS.
A typical desktop with 8 cores and 16 GB RAM should be enough to run most of the analyses, although if running the workflow with these specs, it might take a long time to perform every step.
However, if running ``search`` mode with the flag ``--hlaPeptidomics`` (Fig. 6 of Rp3 manuscript), make sure you have at least 32 or 64 GB ram
available. This is because the already bloated proteogenomics search space gets even bigger with the addition of
unspecific cleavage required for HLA peptidomics. This will drastically increase the complexity of the analysis.


Install Rp3 and its dependencies
----------------

Typical installation time will vary depending on how many dependencies requirements are already met. Downloading the release from the GitHub page should not take more than 5 min. at average connection speed, and installing all dependencies should take no more than 20 min.

1. Download the latest version of the pipeline from its GitHub releases page.
2. ``cd`` to the directory containing the .tar.gz file and run ``tar -xzvf`` on the terminal.
3. Install required Python packages
  	1. Install Python3.10. Although some backward compatibility is expected, other versions of Python haven't been tested.
	2. ``$ cd`` to the RP3 folder and install requirements

.. code-block:: console
   $ pip install -r requirements.txt

5. Install dependencies
To avoid incompatibility, make sure to have installed the versions for each tool that was used during the development of RP3. You can install alternative versions at your own risk. Compatibility is not guaranteed.

Dependencies
----------------

| Tool          | Mode                                     | Version          | Purpose   | Link                                                                                                   |
|--------------|------------------------------------------|------------------|-----------|--------------------------------------------------------------------------------------------------------|
| MSFragger    | search, rescore                          | 3.5              | Match MS spectra against protein database   | [Download](https://msfragger.nesvilab.org/)                                                            |
| Percolator    | search, postms, rescore                  | 3.06.1           | Post-process MSFragger results and infer FDR | [Download](percolator.ms)                                                                              |
| NCBI Blast	| search (--refseq), anno (--conservation) | 2.12.0	      | Local alignment of microproteins | [Download](https://ftp.ncbi.nlm.nih.gov/blast/executables/blast+/2.12.0/ncbi-blast-2.12.0+-src.tar.gz) |
| STAR			| 	        ribocov, rna                    | 2.7.4a	      | Alignment of short-reads to the genome in a splice-aware manner | [Download](https://github.com/alexdobin/STAR/archive/2.5.3a.tar.gz)                                    |
| StringTie		| 	             rna                        | 2.2.1 	      | Reference-guided transcriptome assembly | [Download](https://github.com/gpertea/stringtie/releases/tag/v2.2.1)                                   |
| Subread		| 	                  ribocov               | 1.6.3	      | Contains featureCounts, used to perform read counting for Ribo-Seq data in the ``ribocov`` mode | [Download](https://sourceforge.net/projects/subread/files/subread-1.6.3/)                              |
| samtools		| 	                    ribocov, rna        | 1.18	      | Performs a variety of data processing for ``.sam`` and ``.bam`` files | [Download](https://github.com/samtools/samtools/releases/tag/1.18)                                     |
| MSBooster		| 	                  search, rescore       | 1.2.1	      | Predicts Retention Times and add delta_RT_loess to ``.pin`` files coming from MSFragger | [Download](https://github.com/Nesvilab/MSBooster/releases/tag/v1.2.1)                                  |


6.  **Important**. Configure the Paths to each of the dependencies in the file config.txt located inside the RP3 folder. Replace the $PATH to each tool **executable** in its respective column. By default, the pipeline will look for the tools in your $PATH.

Testing the installation
----------------

We provide a *demo* mode with reduced datasets so the user can check if the installation is working properly. This mode will check the 5 main modes (*translation, database, search, postms,* and *ribocov*).
- First, you need to download the demo_data from the releases page and put it in the same directory as the Rp3.py script.
- Then, to check all modes at once, simply run

.. code-block:: console

   $ rp3.py demo --threads 8 --outdir demo_outdir

This will use 8 threads to test all 5 main modes of the RP3 pipeline. Typical run time to test every mode is ~30-50 min, but can vary depending on available computational resources. The whole workflow of the pipeline is time-consuming because it has to deal with multiple types of omics datasets.
The output files will be generated at `demo_outdir`, or another specified directory.
- If you want to skip a mode during testing, pass the argument --skip_database, for instance. All parameters available for the ``demo`` mode can be checked with ``rp3.py demo -h``.

- If testing also the Ribocov mode, you will need to provide STAR indexes for the hg19 genome. These are very large files and we make them available for download in this box folder: https://salkinstitute.box.com/s/5uyf0tdfm7w1zgx3kqr1u367zlg9ocoy. Alternatively, you can use your own indexes. For demo mode, make sure to put these indexes inside the sofware directory under the folder ``STAR_indexes``.
Test dataset
----------------

The test data is composed of single files (to enable fast testing of the software's core functionalities) from studies used in the published manuscript. For each mode:
``search``: the mzML file 20130328_EXQ1_MiBa_SA_HCC1937.mzML from MassIVE (accession MSV000089022).
``ribocov``: SRR8449580.fastq file containing Ribo-Seq reads from GEO Series (GSE198109).
Additionally, reference annotation files are included in the testing datasets. These are used for nearly every mode of the pipeline:
Reference GTF, rRNA and tRNA fasta, and genome Fasta files from hg19 assembly from UCSC. Human RefSeq from latest assembly from NCBI. Human reference proteome from Uniprot.

Every file is located inside the `demo_data` directory, located inside the Rp3 directory. If you want to download the demo_data, get it from the release page, separately from the source code.

