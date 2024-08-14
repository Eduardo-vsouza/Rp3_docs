Welcome to the Rp3 documentation
===================================

Rp3 (Ribosome Profiling and Proteogenomics pipeline) was developed to integrate the analyses for three different multi-omics techniques: RNA-Seq, Ribo-Seq and Proteogenomics. Its overarching goal is to identify novel microproteins (shorter than 100/150 aa) encoded by small Open Reading Frames (smORFs). Then, it will check for translational evidence in the Ribo-Seq data for these novel smORFs.

### Ribo-Seq 
Ribosome Profiling, or Ribo-Seq, is a widely used technique for the identification of novel coding genes, as it provides direct evidence of translation. It consists of the sequencing of fragments of mRNA that are being actively read by the ribosome (RPFs). Pipelines usually map these RPFs to transcripts from a de novo or reference-guided transcriptome assembly to check for the existence of novel transcripts that might carry smORFs (or any gene) actively being translated by the ribosome. Here, the Ribo-Seq reads will be used to check for translational evidence of novel microprotein-encoding smORFs identified by the Proteogenomics part of the pipeline (Fig. 1 steps VI and VII). 


Check out the :doc:`usage` section for further information, including
how to :ref:`installation` the project.

.. note::

   This project is under active development.

Contents
--------

.. toctree::

   installation
   quick_start
   modes




