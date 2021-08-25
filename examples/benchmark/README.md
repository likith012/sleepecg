# Heartbeat detection benchmarks
This example reproduces the benchmarks shown in the main [`README.md`](https://github.com/cbrnr/sleepecg#readme).

## Usage
To run the benchmark, create a virtual enviroment and install the requirements using
```
pip install -r requirements-benchmark.txt
```

and execute
```
python benchmark_detectors.py <benchmark>
```
where `<benchmark>` is a top-level key in `config.yml` (see section [Configuration](#configuration)). Possible values are `runtime`, `metrics`, and `rri_similarity`.

Plots can be created by executing
```
python plot_benchmark_results.py <results.csv>
```
which will save the plot to `<results>.svg` at the same location as `<results>.csv`. Plot types and labels for the provided benchmarks are selected based on the filename, so renaming may lead to errors.


## Configuration
A benchmark configuration is specified below a unique top-level key in `config.yml`.
|Key|Type|Default|Description|
|---|----|--------|-----------|
|`data_dir`|`str`|`'~/.sleepecg/datasets'`|Path where all datasets are stored. Required files will be downloaded if they don't exist.|
|`outfile_dir`|`str`|`'.'`|Path where the evaluation results should be stored.|
|`db_slug`|`str`||Which dataset to use for evaluation. Possible values: [`mitdb`](https://physionet.org/content/mitdb/1.0.0/), [`ltdb`](https://physionet.org/content/ltdb/1.0.0/), [`gudb`](https://github.com/berndporr/ECG-GUDB).|
|`detectors`|`list[str]`||Detectors to be evaluated. For possible options, see [`utils._detector_dispatch`](https://github.com/cbrnr/sleepecg/blob/main/examples/benchmark/benchmark_detectors.py).|
|`signal_lengths`|`list[int]`||Length in minutes to which each ECG signal should be sliced. If a signal is too short, it is skipped.|
|`max_distance`|`float`|`0.1`|Maximum temporal distance in seconds between detected and annotated beats to count as a successful detection.|
|`suppress_warnings`|`bool`|`False`|Whether to suppress warnings during detector execution.|
|`calc_rri_similarity`|`bool`|`False`|Whether to calculate similarity measures between detected and annotated RR intervals (computationally expensive for long signals).|

## Known issues
- `heartpy` detection will fail for mitdb:105:V1. It _raises_ a `BadSignalWarning`, which is caught in `utils.evaluate_single`.
- For signal lengths starting somewhere between 600 and 900 minutes, the `heartpy` detector takes at least several hours for ltdb:15814:ECG2.
- For signal lengths starting somewhere between 300 and 600 minutes, `wfdb-xqrs` takes at least 20 times longer for ltdb:14134:ECG2 and ltdb:14184:ECG2 than for the other ltdb records.