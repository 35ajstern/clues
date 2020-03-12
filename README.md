# clues

## Basic usage

`
python inference.py --times example/example --out example
`

where 

`example/example.{der,anc}.npy` are NumPy binary files containing derived/ancestral coalescence times. These are estimated using Relate [Speidel et al 2019]).

## Options

### Population size changes

While `clues` assumes a panmictic population, there is an option to modify constant population size (`-N`) or population size changes (`--coal`). See the example folder for an example .coal file. The .coal file is the same format as outputted by `Relate`: https://myersgroup.github.io/relate/modules.html#PopulationSizeScript_FileFormats. **Note**: while the `-N` option assumes diploid Ne, the `--coal` option assumes haploid Ne (as in `Relate`). 

### Timing of selection

#### Time bins

This option can allow you to specify (a) specific time(s) to test for selection:

Usage: `--timeBins example/timeBins.txt`

`timeBins.txt` is a file that denotes epochs (e.g. 0-50 gens before present) during which selection is allowed to differ from 0 during optimization. Note that you can set arbitrarily many time bins (e.g., 0,50,100,150...), but the estimator error will increase drastically, and optimization duration will also increase.

#### Cutoff time

This option specifies the time to 'cut off' the coalescence process. (Default 1000 gens before present). Note this must exceed the oldest edge of the time bins.

Usage: `--tCutoff 1000`

### Importance sampling

The options `--burnin <LEN_BURNIN>` and `--thin <LEN_THIN>` are used to modulate which MCMC samples (from `Relate/SampleBranchLengths.sh`) to include in the importance sampling estimate.

### Ancient samples

Usage: `--ancientSamps example/exampleAncientSamps.txt`

`exampleAncientSamps.txt` is a file listed ancient genotype likelihoods (set likelihoods to 0/-inf if hard-called)


`timeBins.txt` is a file that denotes epochs (e.g. 0-50 gens before present) during which selection is allowed to differ from 0 during optimization. Note that you can set arbitrarily many time bins (e.g., 0,50,100,150...), but the estimator error will increase drastically, and optimization duration will also increase.

**Note**: you can use ancient samples AND/OR coal times! I.e. it is valid to run the HMM on only aDNA samples, if you want.

## Output

This will save three files: (1) `example.epochs.npy`, (2) `example.freqs.npy`, (3) `example.post.npy`. These files are discrete sets of (1) timepoints and (2) allele frequencies, as well as (3) the posterior distribution on allele frequency as a function of time. The posterior is saved in log space. All of these files can be loaded in python using `np.load()`. To get the max-marginal estimate of the allele frequency trajectory, simply load the posterior and take the maximum at each slice along the time axis (columns). To get the posterior mean, simply take the mean of `freqs` with respect to these slices. 
