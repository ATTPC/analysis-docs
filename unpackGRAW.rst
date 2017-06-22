1) Merge and Unpack GRAW Files into HDF5 Files
==============================================

Installing and Compiling
------------------------

This step in the analysis should be performed locally. Install the graw-merger tool and it's dependencies. The graw-merger repository contains the source code for the `graw2hdf` tool, which can be used to merge the GRAW files produced by the GET electronics into an `HDF5` <https://www.hdfgroup.org/HDF5/>`__ file. Armadillo should already be installed, but some `Boost C++ libraries <http://www.boost.org/>`__ are are required for this step. Use wget to download to download the .tar archive (vesion 1.55 or later) and extract it (find the correct download `here <https://dl.bintray.com/boostorg/release/1.64.0/source/>`__):

.. note::
   
   You may wish to use Homebrew to install the boost-compute package if running macOS.

.. code-block:: shell

   wget https://dl.bintray.com/boostorg/release/1.64.0/source/boost_1_64_0.tar.gz
   tar xzf boost_1_64_0.tar.gz
   cd boost_1_64_0

See the index.html file in the current directory for more information on the current boost version; the "Getting Started" section (5) contains useful installation instructions, but the neccesary procedure is outlined here. Here we build all Boost libraries for simplicities sake, see graw-merger's readme for the specific libraries necesary. Be sure to provide the `--with-python-version=` flag to 

.. code-block:: shell 

   ./bootstrap.sh --with-python-version=3.6
   sudo ./b2 install


Now, clone the graw-merger repository from GitHub which, can be found `here <https://github.com/ATTPC/graw-merger>`__:

.. code-block:: shell

   git clone https://github.com/ATTPC/graw-merger.git
   cd graw-merge

More information on installation and compilation can be found in the README.md file packaged with the software, but the instructions are outlined here:

.. code-block: shell

   mkdir build && cd build
   cmake -DCMAKE_BUILD_TYPE=Release ..
   make
   make install  # sudo might be required


Usage
-----

`graw2hdf` can be used as follows:

.. code-block:: shell

   graw2hdf [-v] --lookup LOOKUP INPUT [OUTPUT]

The `lookup` argument takes the path to the pad map lookup table, as csv. The `INPUT` positional argument should be the path to a directory containing GRAW files for a run. The `OUTPUT` argument is the path where the output HDF5 file should be created. If no output path is given, a file will be created next to the `INPUT` directory with the same name as that directory and the extension `.h5`.

The following command will display this information in the terminal (also a good installation test):

.. code-block:: shell

   graw2hdf --help
