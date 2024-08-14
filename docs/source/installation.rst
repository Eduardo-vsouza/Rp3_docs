Usage
=====

.. _installation:

Installation
------------

Typical installation time will vary depending on how many dependencies requirements are already met. Downloading the release from the GitHub page should not take more than 5 min. at average connection speed, and installing all dependencies should take no more than 20 min.

1. Download the latest version of the pipeline from its GitHub releases page.
2. ``cd`` to the directory containing the .tar.gz file and run ``tar -xzvf`` on the terminal.
3. Install required Python packages
  	1. Install Python3.10. Although some backward compatibility is expected, other versions of Python haven't been tested.
	2. ``$ cd`` to the RP3 folder
	3. ``$ pip install -r requirements.txt
5. Install dependencies
	To avoid incompatibility, make sure to have installed the versions for each tool that was used during the development of RP3. You can install alternative versions at your own risk. Compatibility is not guaranteed.

.. code-block:: console

   (.venv) $ pip install lumache


