Controlling the FDR
=====

.. _fdr:

Rp3 provides a few ways to control the way we infer the FDR.

- You can rescore your proteogenomics results providing the ``--rescore``. This will append the positive results from the first peptide search to the reference proteome and perform a second round of searches with a smaller database, to better control the FDR and reduce false positives.

- If you want to assess the FDR at the protein level, specify ``--proteinFDR`` during the ``search`` mode. By default, the pipeline will assess the FDR at the peptide level.

- Rp3 can also assess the FDR for unannotated microproteins and annotated proteins separately by providing the ``--groupedFDR`` flag. This will likely increase the number of identifications in each group, but will also increase false positives. This flag can be provided during the ``search`` and ``rescore`` mode. If provided in ``search``, it also requires the ``--rescore`` flag.

- MSBooster may also be run after the peptide search and before the post-processing with Percolator by specifying the flag ``--MSBooster``. This will include predicted retention times to the ``.pin`` file from the search with MSFragger used as input for Percolator. This can either increase or reduce identifications, heavily dependant on the dataset. It should make the analysis more robust and we recommend it, however. Requires ``--rescore``.

- If you have already run the ``search`` or ``postms`` mode, and wish to recalculate the FDR by providing a different set of arguments, you can run either mode by providing ``--recalculateFDR`` along with any desired arguments.
