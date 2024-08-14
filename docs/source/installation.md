
## Installation
Typical installation time will vary depending on how many dependencies requirements are already met. Downloading the release from the GitHub page should not take more than 5 min. at average connection speed, and installing all dependencies should take no more than 20 min.

1. Download the latest version of the pipeline from its GitHub releases page.
2. ``cd`` to the directory containing the .tar.gz file and run ``tar -xzvf`` on the terminal.
3. Install required Python packages
  	1. Install Python3.10. Although some backward compatibility is expected, other versions of Python haven't been tested.
	2. ``$ cd`` to the RP3 folder
	3. ``$ pip install -r requirements.txt
5. Install dependencies
	To avoid incompatibility, make sure to have installed the versions for each tool that was used during the development of RP3. You can install alternative versions at your own risk. Compatibility is not guaranteed.

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

	