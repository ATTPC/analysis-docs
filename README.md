[![Documentation Status](https://readthedocs.org/projects/attpc-analysis/badge/?version=latest)](http://attpc-analysis.readthedocs.io/en/latest/?badge=latest)

# Analysis Manual for pytpc

This documentation manual provides steps for installation and use of the [pytpc package](https://github.com/ATTPC/pytpc) that provides Python modules for reading, analyzing, and fitting data from the Active-Target Time Projection Chamber (AT-TPC) at the [NSCL] (http://nscl.msu.edu). The documentation is hosted online by ReadTheDocs [ReadTheDocs](https://attpc-analysis.readthedocs.io/en/latest/) but steps for offline compilation are listed below.

## Dependencies

The following packages are required to use pytpc's analysis-docs:

- sphinx
- sphinx-bootstrap-theme
- latexmk (for latexPDF compilation)

## Recommended compilation procedures

Compile from the base of the analysis-docs repository.

**HTML**

To compile the manual in HTML format enter the following into your command line:
```bash
make clean
make html
```
**PDF**

To compile the manual in PDF format enter the following into you command line:
```bash
make clean
make latexPDF
```
