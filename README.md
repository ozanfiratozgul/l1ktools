# L1000 Analysis Tools v1.0


Copyright 2011-2015 Broad Institute of MIT and Harvard.

A collection of software tools to read and analyze data produced from
the L1000 project ([lincscloud.org](http://www.lincscloud.org)).

## Analysis Tools

A brief description of the tools included in this software package is
given below. The Matlab implementation of the tools is currently the
most mature. Some basic utilities in R and java are also included. We
will update the tools as they become available. Please view the changelog
for changes.

### Matlab Tools: matlab/

#### Requirements:

1. Matlab R2014b and above
2. Statistics Toolbox

#### Setting the MATLAB path:
Enter the "pathtool" command, click "Add with Subfolders...", and select the directory l1ktools/matlab.

### Running the standard CMap data processing pipeline
All scripts are contained within the `matlab/data_pipeline` folder.
A directory of example .lxb files from a LINCS Joint Project (LJP) plate
under the `data/lxb` directory. Note that this process may take up to an hour depending on the machine you're running it on. For those who wish to view outputs without running the pipeline and/or compare their results with CMap's, example pipeline outputs are under `matlab/data_pipeline/results`. These outputs are described in the section below.

```
% setup the envronment
l1kt_setup

% process a full plate of data
[gex_ds, qnorm_ds, inf_ds, zs_ds_qnorm, zs_ds_inf] = process_plate('plate', 'LJP009_A375_24H_X1_B20', 'raw_path', '../data/lxb', 'map_path', '../data/maps');

% convert a directory of LXB files (level 1) into gene expression (GEX, level 2) matrix.
% here, using example data
gex_ds = level1_to_level2('plate', 'LJP009_A375_24H_X1_B20', 'raw_path', '../data/lxb', 'map_path', '../data/maps')

% convert the GEX matrix (level 2) to quantile normalized (QNORM, level 3) matrices
% in both landmark and inferred (INF) gene spaces.
[qnorm_ds, inf_ds] = level2_to_level3('plate', 'LJP009_A375_24H_X1_B20', 'plate_path', '.')

% convert the QNORM matrix (level 3) into z-scores (level 4).
% same procedure can be performed using INF matrix (not shown).
zs_ds = level3_to_level4(qnorm_ds, 'plate', 'LJP009_A375_24H_X1_B20', 'plate_path', '.')

```
*** Note: *** Because the peak detection algorithm is non-deterministic, it's possible that data in levels 2 through 4 could differ slightly for two instances of processing the same plate. The code allows reproducing a previous run by passing a random seed file to the `process_plate` script. We provide such a file at `resources/rndseed.mat`. Reproducing the results provided in matlab/data_pipeline/results can be done as follows:

```
% reproduce provided results
[gex_ds, qnorm_ds, inf_ds, zs_ds_qnorm, zs_ds_inf] = process_plate('plate', 'LJP009_A375_24H_X1_B20', 'raw_path', '../data/lxb', 'map_path', '../data/maps', 'rndseed', 'resources/rndseed.mat');
```

#### Description of Pipeline Outputs

| File | Description |
| ---- | ----------- |
| LJP009_A375_24H_X1_B20.map | Sample annotations file |
| LJP009_A375_24H_X1_B20_COUNT_n384x978.gct | Matrix of analyte counts per sample|
| LJP009_A375_24H_X1_B20_GEX_n384x978.gct | Level 2 matrix of gene expression (GEX) values|
| LJP009_A375_24H_X1_B20_QNORM_n371x978.gct | Level 3 matrix of normalized expession profiles (landmark space) |
| LJP009_A375_24H_X1_B20_INF_n371x22268.gct | Level 3 matrix of normalized expession profiles (full inferred space)|
| LJP009_A375_24H_X1_B20_ZSPCQNORM_n371x978.gct | Level 4 matrix of differential expression signatures (landmark space) |
| LJP009_A375_24H_X1_B20_ZSPCINF_n371x22268.gct | Level 4 matrix of differential expression signatures (full inferred space) |
| dpeak | folder containing peak detection outputs and QC |
| liss | folder containing LISS outputs and QC |
| quantile_plots.png | Plot of the normalized and non-normalized expression quantiles |


---
#### Other Tools and Demos (under matlab/demos_and_examples):
* [**l1kt_dpeak.m**](https://github.com/cmap/l1ktools/blob/master/matlab/l1kt_dpeak.m): Performs peak deconvolution for all analytes in a single LXB file, and outputs a report of the detected peaks.
* [**l1kt_plot_peaks.m**](https://github.com/cmap/l1ktools/blob/master/matlab/l1kt_plot_peaks.m): Plots intensity distributions for one or more analytes in an LXB file.
* [**l1kt_parse_lxb.m**](https://github.com/cmap/l1ktools/blob/master/matlab/l1kt_parse_lxb.m):	Reads an LXB file and returns the RID and RP1 values.
* [**l1kt_liss.m**](https://github.com/cmap/l1ktools/blob/master/matlab/l1kt_liss.m): Performs Luminex Invariant Set Smoothing on a raw (GEX) input .gct file
* [**l1kt_qnorm.m**](https://github.com/cmap/l1ktools/blob/master/matlab/l1kt_qnorm.m):	Performs quantile normalization on an input .gct file
* [**l1kt_infer.m**](https://github.com/cmap/l1ktools/blob/master/matlab/l1kt_infer.m):	Infers expression of target genes from expression of landmark genes in an input .gct file

See the documentation included with each script for a details on usage
and input parameters.

#### Demo:
* [**dpeak_demo.m**](https://github.com/cmap/l1ktools/blob/master/matlab/dpeak_demo.m): Demo of peak detection. To run the demo, start Matlab, change to the folder containing dpeak_demo and
type dpeak_demo in the Command Window. This will read a sample LXB
file (A10.lxb), generate a number of intensity distribution plots and create a
text report of the statistics of the detected peaks (A10_pkstats.txt).

* [**example_methods.m**](https://github.com/cmap/l1ktools/blob/master/matlab/example_methods.m): Reads in a .gct and a .gctx file, z-score the data in the .gctx file, and read in an .lxb file. To run the demo, start Matlab, change to the folder containing example_methods and type example_methods at the command line.

---
### R Tools: R/

#### Requirements:

1. R versions 3.1 and above
2. prada package: http://www.bioconductor.org/packages/devel/bioc/html/prada.html
3. rhdf5 package: http://bioconductor.org/packages/release/bioc/html/rhdf5.html

#### Tools:

R tools are found under R/cmap

* [**lxb2txt.R**](https://github.com/cmap/l1ktools/blob/master/R/cmap/lxb2txt.R):	Saves values from an LXB file as a tab-delimited text file.
* [**lxb2txt.sh**](https://github.com/cmap/l1ktools/blob/master/R/cmap/lxb2txt.sh): Bash wrapper to lxb2txt.R 
* [**io.R**](https://github.com/cmap/l1ktools/blob/master/R/cmap/io.R): Classes for reading and writing .gct / .gctx files

#### Demo:
* [**example_methods.R**](https://github.com/cmap/l1ktools/blob/master/R/example_methods.R): To run the demo, change to the folder containing example_methods.R and source the script. It will read in a .gctx file and display its contents.

---
### Java Tools: java/

#### Tools:

* [**GctxDataset.java**](https://github.com/cmap/l1ktools/blob/master/java/src/l1ktools/cmap/GctxDataset.java): Class to store a GCTX data set.
* [**GctxReader.java**](https://github.com/cmap/l1ktools/blob/master/java/src/l1ktools/cmap/GctxReader.java): Class to read a GCTX file.
* [**LXBUtil.java**](https://github.com/cmap/l1ktools/blob/master/java/src/l1ktools/cmap/LXBUtil.java): Class to read and store .lxb files.

#### Demo:
* [**ReadGctxExample.java**](https://github.com/cmap/l1ktools/blob/master/java/examples/ReadGctxExample.java): To run the demo, change to the java/examples folder, then compile by running sh compileExamples.sh, then run by running the runExample*.sh file that alligns with your OS. 
* 

---
### Python Tools: python/

#### Requirements:

1. Python 2.7 (untested under Python 3)
2. numpy: [http://numpy.scipy.org](http://numpy.scipy.org)
3. pandas: [http://pandas.pydata.org/](http://pandas.pydata.org/)
4. requests: [http://docs.python-requests.org/en/latest/](http://docs.python-requests.org/en/latest/)
5. pytables: [http://www.pytables.org/moin](http://www.pytables.org/moin)
6. blessings: [http://pypi.python.org/pypi/blessings](http://pypi.python.org/pypi/blessings)

#### Setting the Python path:
To run, append l1ktools/python to the PYTHONPATH environment variable.

#### Tools:
* [**cmap/io/gct.py**](https://github.com/cmap/l1ktools/blob/master/python/cmap/io/gct.py) : Classes to interact with .gct and .gctx files.
* [**cmap/util/api_utils.py**](https://github.com/cmap/l1ktools/blob/master/python/cmap/util/api_utils.py): Classes to make calls to the LINCS annotation API and return results as Python data structures.

#### Demo:
* [**example_methods.py**](https://github.com/cmap/l1ktools/blob/master/python/example_methods.py): To run the demo, change to the folder containing example_methods.py and run the script. It will read in a .gctx file, display its contents, and write to disc.

---
## Common data analysis tasks


### Reading .gct and .gctx files
* **MATLAB**: Use the [parse_gctx](https://github.com/cmap/l1ktools/blob/master/matlab/parse_gctx.m) function.
* **R**: Source the script [l1ktools/R/cmap/io.R](https://github.com/cmap/l1ktools/blob/master/R/cmap/io.R). Then use the parse.gctx function.
* **Python**: Import the module [cmap.io.gct](https://github.com/cmap/l1ktools/blob/master/python/cmap/io/gct.py). Then instantiate a GCT object, and call its read() method. For more information, see the documentation on the GCT class.
* **Java**: See [ReadGctxExample.java](https://github.com/cmap/l1ktools/blob/master/java/examples/ReadGctxExample.java) for an example.

### Creating .gct and .gctx files
* **MATLAB**: Use the [mkgct](https://github.com/cmap/l1ktools/blob/master/matlab/lib/mkgct.m) and [mkgctx](https://github.com/cmap/l1ktools/blob/master/matlab/lib/mkgctx.m) functions.
* **R** Source the script [l1ktools/R/cmap/io.R](https://github.com/cmap/l1ktools/blob/master/R/cmap/io.R). Then use the write.gctx or write.gct functions.
* **Python**: Import [cmap.io.gct](https://github.com/cmap/l1ktools/blob/master/python/cmap/io/gct.py) and instantiate a GCT object. Then call the "build" method or "build_from_DataFrame" method to assmble a GCT object from a data matrix and optionally row and column annotations. Finally, call the "write" method to write to file as a .gctx.

### Z-Scoring a data set
* **MATLAB**: Use the [robust_zscore](https://github.com/cmap/l1ktools/blob/master/matlab/robust_zscore.m) function. Also see the [example_methods.m](https://github.com/cmap/l1ktools/blob/master/matlab/example_methods.m) script.

### Reading / converting .lxb files
* **MATLAB**: To read an .lxb into the MATLAB workspace, use the [l1kt_parse_lxb](https://github.com/cmap/l1ktools/blob/master/matlab/l1kt_parse_lxb.m) function.
* **R**: To convert an .lxb file to text, use the [R/cmap/lxb2txt.sh](https://github.com/cmap/l1ktools/blob/master/R/cmap/lxb2txt.sh) script.

---

## The LINCS API

The CMAP Cloud API offers programmatic access to annotations and perturbational signatures in [the LINCS L1000 dataset](http://lincscloud.org/) via a collection of HTTP-based RESTful web services. These services support complex queries via simple HTTP GET requests that can be executed in a web browser or any programming language. The results are returned as standard JSON objects. Click on the links on the left for usage instructions and examples.

### Interacting with the API

In order to make the API call, an API key must be provided. If you do not have a key, contact lincs@broadinstitute.org.

* **Lincscloud website**: To view the available services and live examples, visit [http://api.lincscloud.org/](http://api.lincscloud.org/).
* **Programmatic access via Python**: Import the module [cmap.util.api_utils](https://github.com/cmap/l1ktools/blob/master/python/cmap/util/api_utils.py). The classes CMapAPI and APIContainer handle calls to the API; see their documentation for more details. See example_methods.py for an example API call.

#### Python demo:
* [**api_examples.py**](https://github.com/cmap/l1ktools/blob/master/python/api_examples.py): To run the demo, change to the folder containing api_examples.py and run the script. It will make calls to the API and store their results as Python data structures.

---

## Software License

This software is restricted to research use only within academic, not-for-profit institutions.

For licensing information see [lincscloud.org/license/](http://lincscloud.org/license/).
