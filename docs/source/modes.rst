Modes
=====

.. _modes:

The Rp3 pipeline has different modes responsible for different steps in the data processing workflow.
Run the modes in the adequate order using the same output directory provided with ``--outdir``. Some modes are 'extra'
and will provide additional layers of information about the identified microproteins.

Database
------------

1. Run the RP3 pipeline in its ``database`` mode to generate a custom database for mass spectrometry-based proteomics. Run the pipeline with ``$ rp3.py database -h`` to print all available commands for this mode.

2. Run the database mode
	 - Alternatively, you can run the ``translation`` mode separately, in case you do not need the reference proteome, decoy sequences and contaminants added to the fasta file. You can always run the ``database`` mode with the ``--skip_translation`` flag later on to generate a database suited for peptide search for mass spectrometry data.
	 - By default, ``database`` automatically executes the ``translation`` mode.

3. Parameters
	Run ``$ rp3.py database -h`` on the command line to check which commands are available. This will print out this message:

.. code-block:: console

    General Parameters:
      database
      --outdir OUTDIR, -o OUTDIR
                            Inform the output directory (default: foopipe)
      --threads THREADS, -p THREADS
                            Number of threads to be used. (default: 1)

    database options:
      --proteome PROTEOME   Reference proteome (default: None)
      --genome GENOME
      --gtf_folder GTF_FOLDER
      --external_database EXTERNAL_DATABASE
      --skip_translation



Example code
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

	4.1. Generating a custom database for proteomics from a transcriptome assembly or custom GTF files

	``$ rp3.py database --outdir <path/to/output/directory> --threads 8 --genome <path/to/genome.fasta> --gtf_folder <path/to/gtf/folder> --proteome <path/to/reference_proteome.fasta``

	This will tell the pipeline to generate databases in the ``databases`` directory inside the output directory provided with ``--outdir``.

	**Remember to always specify the same output directory in the other modes, as each step is dependent on the previous one. **

	4.2. In case you already have your own database with putative sequences, you can provide it to the pipeline with the ``--external_database`` flag along with the reference proteome. In that case, the pipeline will append the reference proteome to your fasta file along with the decoys and contaminants. It will then tag each sequence for the subsequent steps. This is useful when not translating a transcriptome assembly to the 3-reading frames, like when searching for mass spectrometry evidence for microproteins identified with Ribo-Seq.

	2.3. Notes
		- ``--gtf_folder`` expects a folder with one or more GTF files or the path to a GTF file.

5. Output files
	Database files will be generated inside the output directory in the ``databases`` folder. You can check for database metrics at ``metrics/database_metrics.txt``.

Notes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Unless specified in the next modes, databases will have annotated proteins excluded based on the ``-proteome`` provided. This behavior is intended to remove, by default, any already annotated microprotein and peptides that match to them. If you are interested in annotated proteins as well, there are two ways to prevent this from happening:
- Specify the parameter ``--uniprotAnnotation`` during the ``database`` mode. This will use a data frame obtainable from Uniprot containing the annotation level for each protein in the ``--proteome``. Rp3 will then tag differently each protein in the provided proteome based on their annotation level. As proteins in Uniprot vary in their annotation, it might be wise to include those with very low annotation levels when identifying unannotated proteins, as they are poorly characterized. The annotation level ranges from 1 to 5. To control which should be kept, provide the argument ``--annotationLevel`` followed by a number from 1 to 5. ``--annotationLevel 4`` will keep only those proteins with an annotation level equal to or lower than 4. This is the default behavior if ``--uniprotAnnotation`` is provided. **Requires ``--includeLowAnnotation`` to be specified during the ``search`` mode.**

- Specify ``--keepAnnotated`` in ``search`` mode, the next step. This will treat every protein, annotated or unannotated, the same.

Peptide search
------------

1. During this step, the pipeline will use the database generated in the ``database`` to look for evidence of microproteins in the mass spectrometry data.
2. Make sure to have the data stored in the centroided ``.mzML`` format. In case it isn't, use the tool msconvert from the ProteoWizard suite (https://proteowizard.sourceforge.io/).
3. By default, the ``search`` mode will run the ``postms`` mode as well, which employs Percolator to assess the FDR and performs the necessary cutoffs. You can run the ``search`` mode by itself by specifying the flag ``--no_post_process``. In that case, only MSFragger will be run and you will have ``.pin`` files generated from the search. You can always run the ``postms`` mode later on to process the search files.
4. Requirements
	1. Raw data from label-free LC-MS/MS experiments (``mzML``).
	2. Database generated by the ``database`` mode OR provided with the flag ``--external_database``.
5. Parameters
	Run ``$ rp3.py search -h`` at the terminal to print this message:

.. code-block:: console

    General Parameters:
      search
      --outdir OUTDIR, -o OUTDIR
                            Inform the output directory (default: foopipe)
      --threads THREADS, -p THREADS
                            Number of threads to be used. (default: 1)

    search options:
      --mzml MZML
      --digest_max_length DIGEST_MAX_LENGTH
      --digest_min_length DIGEST_MIN_LENGTH
      --std_proteomics
      --quantify
      --mod MOD
      --create_gtf
      --cat                 Perform the search using a concatenated target and decoy database.
                            Requires the databases to be generated using the 'cat' flag.
                            (default: False)
      --tmt_mod TMT_MOD
      --fragment_mass_tolerance FRAGMENT_MASS_TOLERANCE
      --refseq REFSEQ
      --groups GROUPS       Tab-delimited file associating a database with a raw file. Should
                            contain two columns: files, groups. Groups should have the same name
                            as the generated databases. If not specified, the pipeline will
                            search every raw file using every GTF file provided. (default: None)


Folder organization
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``--mzml`` flag expects the mzml folder to contain groups, such as:

.. code-block:: console
    mass_spec_folder/
        ├── group_1/
        │   └── lc_ms-ms_1.mzML
        ├── group_2/
        │   └── lc_ms-ms_2.mzML
        └── group_3/
            └── lc_ms-ms_3.mzML
            └── lc_ms-ms_4.mzML


In case you have a single group/condition, put all the mzML files inside the ``--mzml`` directory, such as:

.. code-block:: console

    mass_spec_folder/
        └── lc_ms-ms_1.mzML
        └── lc_ms-ms_2.mzML
        └── lc_ms-ms_3.mzML
        └── lc_ms-ms_4.mzML


In this case, Rp3 will automatically detect that the files come from a single group and should be searched together.



Notes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Always specify the same ``--outdir`` previously used for the other modes.
Extra parameters
- The ``--refseq`` parameter will accept a fasta file containing a reference annotation, such as the one from NCBI RefSeq. This is used as an extra sanity check to make sure we remove all annotated microproteins, even those that might be missed by the pipeline in case the provided reference proteome (from Uniprot, for instance) is not comprehensive enough. This parameter is optional, but recommended.
- You can also include the execution of  ``rescore`` mode following the search and post-processing with Percolator. To do so, specify the flag ``--rescore`` and provide the path to the proteome fasta file with ``--proteome``. The proteome should be the same one used to generate the database in the first step. The proteome is required if rescoring the results, but not for the first round of searches. For details, see [Post-processing](#post-processing)
- The flag ``--MSBooster`` will generate a spectral library with predicted retention times (RT) and delta RT loess compared to the experimental data. These values will be incorporated in the ``.pin`` file used as input for post-processing with Percolator. This can either increase or reduce the number of identifications depending on the analysis, but should improve confidence.


Example code
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: console

	$ rp3.py search --mzml /path/to/mass/spec/folder --outdir path/to/output/directory/ --threads 8 --MSBooster --rescore --proteome path/to/proteome.fasta

Output files
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

	RP3 will produce output files in fasta format for each of the provided groups. Look for them inside the output directory at ``summarized_results/group_name``. Merged files from all the groups are located inside ``summarized_results/merged``.


Post-processing
---------

1. The RP3 pipeline contains a re-scoring mode called ``rescore``. This is intended to perform a second round of searches, now using as a proteomics database the results from the first proteogenomics search (the fasta file generated by the ``search`` mode) appended to the reference proteome. This is useful because the FDR assessment from the first search is not very accurate, as the database generated from the three-frame translation of the transcriptome contains millions of predicted sequences. This bloated database results in false positives and false negatives during FDR assessment. To correct for this, we select the hits at an FDR < 0.01 from the first search and look for them again, now with a smaller database to obtain more accurate hits. This mode will reduce the final number of novel microproteins.
2. This mode can be run during ``search`` mode as well.
3. If the flag ``--rescore`` is not specified during the ``search`` mode, it is also possible to perform the re-scoring
 on the previous run. After running the ``search`` mode, run the ``rescore`` in the same output directory:


.. code-block:: console

	$ rp3.py rescore --outdir /path/to/output/directory --threads 8 --mzml /path/to/mzmz/files --proteome /path/to/reference/proteome --msPattern mzML
Notes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- The ``--msPattern`` specifies the format of the files (usually mzML or bruker (.d) format).

Output files
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Look for output files in fasta and gtf format in the ``rescore/summarized_results`` directory inside the output directory.

Ribocov
------------
This mode will check for Ribo-Seq coverage for the microproteins identified with proteogenomics. To do so, it will run featureCounts on a custom GTF file automatically generated by the pipeline. The available parameters are:

.. code-block:: console

    General Parameters:
      ribocov
      --outdir OUTDIR, -o OUTDIR
                            Inform the output directory (default: None)
      --threads THREADS, -p THREADS
                            Number of threads to be used. (default: 1)

    ribocov options:
      --fastq FASTQ         Provide the path to the folder containing fastq files
                            to be aligned to the genome. If the --aln argument is
                            provided, this is not necessary. (default: None)
      --gtf GTF             Reference gtf file containing coordinates for
                            annotated genes. The novel smORFs sequences from the
                            proteogenomics analysis will be appended to it.
                            (default: None)
      --genome_index GENOME_INDEX
                            Path to the genome STAR index. If not provided, it
                            will use the human hg19 index available at /data/
                            (default: None)
      --cont_index CONT_INDEX
                            STAR index containing the contaminants (tRNA/rRNA
                            sequences). Reads mapped to these will be excluded
                            from the analysis. (default: None)
      --aln ALN             Folder containing bam or sam files with Ribo-Seq reads
                            aligned to the genome. In case this is provided,
                            indexes are not required and the alignment step will
                            be skipped. (default: None)
      --rpkm RPKM           RPKM cutoff to consider whether a smORF is
                            sufficiently covered by RPFs or not. (default: 1)
      --multimappings MULTIMAPPINGS
                            max number of multimappings to be allowed. (default:
                            99)
      --adapter ADAPTER     Provide the adapter sequence to be removed. (default:
                            AGATCGGAAGAGCACACGTCT)
      --plots
      --fastx_clipper_path FASTX_CLIPPER_PATH
      --fastx_trimmer_path FASTX_TRIMMER_PATH



To run the RP3 pipeline on ribocov mode, run:

.. code-block:: console
    rp3.py --outdir path/to/output/directory --threads 8 --gtf path/to/gtf/file --fastq path/to/fastq/folder

This will use the provided genome indexes for the human hg19 assembly located inside the STAR_indexes directory, located inside the rp3 main directory. The user can also generate new indexes if they require to do so. In that case, provide the path to them using the parameters ``--genome_index`` and ``cont_index``. Make sure to change the ``--adapter`` parameter to suit the adapter sequence used for your Ribo-Seq experiment.
The output files will be located inside the ``counts`` directory. They will include a heatmap showing the overall Ribo-Seq coverage for the proteogenomics smORFs, as well as a table containing information about the mapping groups. If the ``--plots`` argument was specified, a plot showing the number of Ribo-Seq-covered smORFs in each mapping group will be generated at ``counts/plots``.

Annotation mode
------------

Rp3 provides an additional mode, ``anno``, to provide additional information on the identified microproteins. Running
Rp3 with ``rp3.py anno --help`` will return:

.. code-block:: console

     ____       _____
    |  _ \ _ __|___ /
    | |_) | '_ \ |_ \
    |  _ <| |_) |__) |
    |_| \_\ .__/____/
          |_|
    RP3 v1.1.0
    usage: /home/microway/PycharmProjects/rp3/rp3.py anno [-h] [--outdir OUTDIR]
                                                          [--threads THREADS]
                                                          [--overwrite]
                                                          [--signalP]
                                                          [--organism ORGANISM]
                                                          [--conservation]
                                                          [--blast_db BLAST_DB]
                                                          [--rescored]
                                                          [--uniprotTable UNIPROTTABLE]
                                                          [--orfClass]
                                                          [--paralogy] [--mhc]
                                                          [--repeats] [--isoforms]
                                                          [--exclusiveMappingGroups]
                                                          [--affinity AFFINITY]
                                                          [--affinityPercentile AFFINITYPERCENTILE]
                                                          [--filterPipeResults]
                                                          [--genome GENOME]
                                                          [--alignToTranscriptome]
                                                          [--maxMismatches MAXMISMATCHES]
                                                          [--gtf GTF]
                                                          [--repeatsFile REPEATSFILE]
                                                          [--refGTF REFGTF]
                                                          anno

    Run pipeline_config in anno mode

    options:
      -h, --help            show this help message and exit

    General Parameters:
      anno
      --outdir OUTDIR, -o OUTDIR
                            Inform the output directory (default: None)
      --threads THREADS, -p THREADS
                            Number of threads to be used. (default: 1)
      --overwrite

    anno options:
      --signalP
      --organism ORGANISM
      --conservation
      --blast_db BLAST_DB
      --rescored            Use this flag if the 'rescore' mode was used to
                            perform a second round of search using the results
                            from the first search. Only the rescored microproteins
                            will be analyzed for conservation in this case.
                            (default: False)
      --uniprotTable UNIPROTTABLE
      --orfClass
      --paralogy
      --mhc
      --repeats
      --isoforms
      --exclusiveMappingGroups

    MHC detection parameters.:
      --affinity AFFINITY
      --affinityPercentile AFFINITYPERCENTILE
      --filterPipeResults

    Paralogy parameters.:
      --genome GENOME
      --alignToTranscriptome
      --maxMismatches MAXMISMATCHES

    ORF Classification parameters.:
      --gtf GTF             reference GTF file. For better accuracy in annotation,
                            this should be a GTF file from Ensembl. They contain
                            more terms that help better classifying the smORF.
                            (default: None)

    Repeats parameters.:
      --repeatsFile REPEATSFILE

    Isoforms parameters.:
      --refGTF REFGTF

With this mode, it's possible to identify signal peptides (running signalP6.0), conservation, orf classes, presence of
MHC epitopes, and presence of paralogs in the genome. To identify signal peptides and annotate smORF classes, run Rp3
as:

.. code-block:: console

    $ rp3.py anno --outdir /path/to/outdir/from/previous/modes --signalP --orfClass --gtf /path/to/ensembl/gtf```

To define smORF classes in the manuscript, we used the annotation from Ensembl, which we believe to be very
comprehensive and allows us to get better insight into our data. To obtain a GTF file from the human genome
assembly hg19, for instance, go to: https://ftp.ensembl.org/pub/grch37/current/gtf/homo_sapiens/ and download the
appropriate GTF file.
