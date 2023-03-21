# Guide for making a simple Nextclade dataset

*Note: This guide is work in progress and is not (yet) thoroughly tested*

## Hello World: making a reference dataset for QC and mutations only

The equivalent of a "Hello World" program when creating a Nextclade dataset is a dataset that only includes a reference sequence and a gene map. That's the bare minimum you need to run Nextclade. With the minimal dataset you can align sequences against your reference and visualize mutations.

For concreteness, we will create a dataset for Zika virus, but the basics are the same for any virus.

### 1. Install the nextclade CLI

While it is possible to test your dataset using the web version of Nextclade at clades.nextstrain.org, it's much more convenient to test your dataset locally using the Nextclade executable on the command line.

See up to date instructions for installation [here](https://docs.nextstrain.org/projects/nextclade/en/stable/user/nextclade-cli.html#installation-local).

This tutorial is based on Nextclade/Nextalign v2. Commands for version 0 and 1 may differ.

Check that you have a working installation by running

```sh
nextclade --version
```

If you struggle with any step in this tutorial or something does not work, please feel free to get in touch with us through one of the following:

- Make a new Github [issue in this repo](https://github.com/neherlab/nextclade-dataset-template/issues/new)
- Make a post in the [Nextstrain discussion forum](https://discussion.nextstrain.org)
- Write an email to [hello@nextstrain.org](mailto:hello@nextstrain.org)

### 2. Choose a well-annotated reference sequence

The choice of reference sequence is important - we will go into more depth later. It is a good default choice to use a reference sequence from [NCBI's refseq database](https://www.ncbi.nlm.nih.gov/labs/virus/vssi/#/virus?SeqType_s=Nucleotide&SourceDB_s=RefSeq). For this tutorial, we choose: [NC_035889](https://www.ncbi.nlm.nih.gov/nuccore/NC_035889).

Make a folder for the dataset, e.g. `hello-world`, and save the reference sequence in FASTA format in this folder as `reference.fasta`.

By this point your dataset should look like this:

```file
hello-world
|
--reference.fasta
```

### 3. Add example sequences for testing

For testing purposes, it's useful to have a few example sequences on which to run Nextclade.

Put them in a separate file named `sequences.fasta`. Make sure to use the FASTA format, with each sequence preceded by a header line that starts with `>`.

[NCBI Virus](https://www.ncbi.nlm.nih.gov/labs/virus/vssi/#/virus?SeqType_s=Nucleotide&VirusLineage_ss=Zika%20virus,%20taxid:64320) is a good place to get some sequences.

```file
hello-world
|
--reference.fasta
--sequences.fasta
```

If you have Nextalign installed, you can already run your first test with:

```sh
nextalign run \
  --input-ref hello-world/reference.fasta \
  --output-all results/hello-world/nextalign \
  hello-world/sequences.fasta
```

The following results should then be available in `results/hello-world/nextalign/`:

```files
results
|
--hello-world
  |
  --nextalign
    |
    --nextalign.aligned.fasta
    --nextalign.errors.csv
    --nextalign.insertions.csv
```

Well done! You've now successfully aligned sequences to a reference.

TODO: Issue to make primers.csv optional
TODO: Issue to not error when passing `--input-dataset` and there is no virus_properties.json
TODO: Issue to not require genemap, or accept genemap without genes
TODO: Issue to not require ref tree

### 4. Add minimal versions of required files

Nextclade v2 requires a few additional files to be present. To get started, just copy or download them from the repo, making sure to save them in `hello-world` under the correct file names:

- [`genemap.gff`](https://raw.githubusercontent.com/neherlab/nextclade-dataset-template/main/hello-world/genemap.gff)
- [`primers.csv`](https://raw.githubusercontent.com/neherlab/nextclade-dataset-template/main/hello-world/primers.csv)
- [`tree.json`](https://raw.githubusercontent.com/neherlab/nextclade-dataset-template/main/hello-world/tree.json)
- [`virus_properties.json`](https://raw.githubusercontent.com/neherlab/nextclade-dataset-template/main/hello-world/virus_properties.json)
- [`qc.json`](https://raw.githubusercontent.com/neherlab/nextclade-dataset-template/main/hello-world/qc.json)

Or just run the following commands:

```sh
curl https://raw.githubusercontent.com/neherlab/nextclade-dataset-template/main/hello-world/genemap.gff > hello-world/genemap.gff
curl https://raw.githubusercontent.com/neherlab/nextclade-dataset-template/main/hello-world/primers.csv > hello-world/primers.csv
curl https://raw.githubusercontent.com/neherlab/nextclade-dataset-template/main/hello-world/tree.json > hello-world/tree.json
curl https://raw.githubusercontent.com/neherlab/nextclade-dataset-template/main/hello-world/virus_properties.json > hello-world/virus_properties.json
curl https://raw.githubusercontent.com/neherlab/nextclade-dataset-template/main/hello-world/qc.json > hello-world/qc.json
```

Because the files contain no real data, you can use them for any virus - there's nothing Zika specific in them, they are just placeholders to make Nextclade happy.

Now your dataset should look like this:

```file
hello-world
|
--reference.fasta
--sequences.fasta
--genemap.gff
--primers.csv
--tree.json
--virus_properties.json
--qc.json
```

### 5. Run Nextclade CLI with your dataset

Now you can run Nextclade on your dataset:

```sh
nextclade run \
  --input-dataset hello-world \
  --output-all results/hello-world/nextclade \
  hello-world/sequences.fasta
```

The following results should then be available in `results/hello-world/nextclade/`:

```files
results
|
--hello-world
  |
  --nextclade
    |
    --nextclade.aligned.fasta
    --nextclade.csv
    --nextclade.insertions.csv
    --nextclade.ndjson
    --nextclade_gene_dummy.translation.fasta
    --nextclade.auspice.json
    --nextclade.errors.csv
    --nextclade.json
    --nextclade.tsv
```

Don't worry about the warning that the `dummy` gene is empty, this is on purpose: we have given Nextclade a minimal `genemap.gff` so that it works for all viruses.

The `nextclade.csv`/`nextclade.tsv` now already contains useful information about the mutations with respect to reference, gaps, Ns, etc.

Nextclade has even produced a minimal Auspice JSON file called `nextclade.auspice.json` that you can visualize by dropping into [auspice.us](https://auspice.us).

Note that Nextclade does not build a tree from your sequences, so they will all attach to the reference node, even if they have a more recent common ancestor. In a future version, we may introduce a tree builder feature.

### 6. Use your dataset in Nextclade web

CLI outputs are nice, but what if you want to use your dataset in Nextclade web for a better visualization? No problem, you have all you need in place:

- Open [clades.nextstrain.org](https://clades.nextstrain.org)
- Click on any dataset (doesn't matter which, e.g. SARS-CoV-2)
- Click on "Customize dataset files"
- Drop the files in each of the fields
- Add your sequences
- Click run

Congratulations: you can now align sequences, visualize their mutations and show basic stats on gaps.

TODO: In web UI show the canonical file names somewhere: `tree.json` (next to Reference tree), or put instead of "drag&drop a file" "drag&drop tree.json file"

### 7. (Optional) Reducing the number of clicks for dataset testing

Manually dragging and dropping all the files is tedious for testing. For simpler dataset testing, you can make a local web server which Nextclade will automatically get your files from, without clicking.

First make sure you have Node.js installed (instructions [here](https://nodejs.org/).

Then you can serve your dataset locally with one simple command:

```sh
npx serve --cors --listen=tcp://0.0.0.0:27722 hello-world
```

If you use the following URL, your dataset in the `hello-world` directory will be automatically loaded: [`https://clades.nextstrain.org?dataset-url=http://localhost:27722`](https://clades.nextstrain.org?dataset-url=http://localhost:27722).

All you need to do is drop the sequences you want to analyze and click run.

If you want to use the example sequences in the dataset, you can avoid any clicks by simply using the following URL: [`https://clades.nextstrain.org?dataset-url=http://localhost:27722&input-fasta=example`](https://clades.nextstrain.org?dataset-url=http://localhost:27722&input-fasta=example).

### 8. (Optional) Sharing your dataset on the internet

TODO

## Original PR from Sidney Bell below
### 3. Make your gene map by filling in the template file `genemap.gff`

Based on the genbank record of your reference sequence, create your `genemap.gff`.

- This is a tab-delimited file, not comma- or space-delimited.
- `seqname` is always the name of your reference sequence
- `source` is always `feature`
- `feature` is always `gene`.
  - Note that for viruses with genomes which encode a polyprotein which is then cleaved into separate `mat_peptide`s, these are listed in genbank records as `matpeptide` features instead of as `gene`s. For these viruses, list each `mat_peptide` feature as a `gene` in the genemap file.
- `score` is irrelevant and can be filled in with a `.` placeholder
- `frame` is important for some viruses which use ribosomal slippage to encode multiple overlapping gene products with the same sequence (e.g., HIV, HCV). This value is always either `0` (default) `1` or `2`.
- `attribute` is always `gene=genenamefoobar`

```
## seqname source feature start end score strand frame attribute
NC_009824 feature gene 340 912 . + 0 gene=C
```

### 4. Update values in `qc.json`

See the [main docs](https://docs.nextstrain.org/projects/nextclade/en/stable/user/algorithm/07-quality-control.html) for an explanation of each QC rule.

As a starting point, we've initialized this file with the values we use for SARS-CoV-2.

### 5. [Optional] Fill in `primers.csv` and/or `virus_properties.json`

If you wish to be alerted when primers change, add these to the template file `primers.csv`. Similarly, if you wish to tweak seed matching parameters or specify clade-specific mutations, add these to `virus_properties.json`. For both of these files, we suggest referring to the `sars-cov-2` reference dataset as an example. This dataset is available to download by running

```
nextclade dataset get --name sars-cov-2 --output-dir foo
```

### 6. Test run!

Try out your new reference dataset by running:

```
nextclade run -D ./reference-files/ -O foo/bar/ input_sequences.fasta
```

Note that while a list of lineages and an output tree will still be provided, it will not be informative and should not be used.

## Optional Extension: create a reference `tree.json` for lineage calling and phylogenetic placements

### 7. Install augur

See instructions [here](https://docs.nextstrain.org/projects/augur/en/stable/installation/installation.html).

### 8. Assemble a sequence dataset that represents the diversity of your pathogen

This dataset does not need to be particularly large (~500 - 1,000 sequences is plenty), but should span the relevant geographic, temporal and genetic diversity as much as possible.
**If you wish to contribute your nextclade reference dataset to the open-science community, make sure to use only data that is publicly available -- i.e., no GISAID or private data!**

We recommend starting with [NCBI Virus](https://www.ncbi.nlm.nih.gov/labs/virus/vssi/#/) and [`augur curate`]().
Don't worry about subsampling yet at this stage; we'll take care of that as part of the tree-building process.

This dataset should consist of two files:

- `prepare-reference-tree/data/metadata.tsv`

  - The only required fields are `strain` `date` and `clade_membership`
  - Including `country` +/- the other geographic fields can be helpful for subsampling later.
  - If `clade_membership` is irrelevant to your purposes, simply fill in this column with any dummy non-null value of your choice.

- `prepare-reference-tree/data/sequences.fasta`, where each sequence is named as `>this/strain/name/foobar`, where strain names correspond to rows in the metadata file.

[See augur docs](https://docs.nextstrain.org/projects/augur/en/stable/faq/metadata.html) for more information on formatting these files

##### Note: if you want to use nextclade to assign clade_membership designations (aka variants / lineages / genotypes / serotypes / subtypes)

Make sure each clade is well-represented in your sequence dataset, and that the `clade_membership` column is filled in for these representative sequences. Add the strain names of these represetative sequences to the file `include.txt`, one per line. It is okay if other sequences in the dataset do not yet have this column filled in.

### 9. Make a copy of your reference sequence in genbank format

The reference sequence should have the same name in both files. These are used for creating your reference tree with augur and assigning ancestral amino acid mutations to internal nodes, which is how nextclade identifies the most appropriate phylogenetic placement for new sequences.

Note that the annotations in the genbank file need to match the annotations in the `genemap.gff` file. For viruses with a genome structure that encodes a polyprotein later cleaved to `mat_peptide`s, you'll need to relabel these features as separate `CDS` and `gene` entries like so:

```
                    gene            340..912
                                    /gene="C"
                    CDS             340..912
                                    /gene="C"
```

Note that the white space in these genbank files is important for parsing and is very difficult to keep track of when editing by hand. Be very careful as you're making these edits!

Places these files into the directory `references-files/` as `reference.fasta` and `reference.gb`

### 10. Build your reference tree

At the top of the file `prepare-reference-tree/Snakefile`, update:

- Line 12: `reference_name = 'NC_009824'` with the name of your reference sequence
- Lines 49-51: subsampling parameters - as appropriate
  - ````group_by = "country year",
        sequences_per_group = 10,
        min_date = 1990```
    ````

Then run the tree build with:

```
cd prepare-reference-tree/
Snakemake .
```

This should create an output tree JSON file in `reference-files/tree.json`. Examine this tree in your browser at `https://auspice.us` to verify that the tree looks reasonable. If relevant, color by `clade_membership` to verify that your clade assignments look correct.

### 11. Test run!

`nextclade run -D ./reference-files/ -O foo/bar/ input-sequences.fasta`

## Optional extension: submit your dataset to help the community! :)

### 12. Clone the nextclade_data repository from github

```
git clone https://github.com/nextstrain/nextclade_data.git
```

### 13. Update descriptive JSON files to help others understand your dataset

- `dataset.json` lists the species name and which reference sequence to use by default for that species
- `datasetRef.json` describes each reference sequence and where it came from
- `tag.json` describes each "release" or version of the reference dataset bundle as a whole

Take a look at the template files provided. As always, if you have questions please feel free to get in touch via our [discussion board](https://discussion.nextstrain.org)!

### 14. Set up your directory structure

For others to use your dataset directly through nextclade, you'll need to restructure your directory a bit so that it looks like:

```
nextclade_data/data/datasets/speciesNameHere/
                              └──dataset.json
                              └──references/accessionNumberHere/
                                            ├──datasetRef.json
                                            └──versions/timeStampHere/
                                                        ├──reference.fasta
                                                        ├──genemap.gff
                                                        ├──primers.csv
                                                        ├──tree.json
                                                        ├──qc.json
                                                        └──tag.json
```

E.g., the path to `reference.fasta` for the `sars-cov-2` dataset looks like:
`nextclade_data/data/datasets/sars-cov-2/references/MN908947/versions/2021-06-25T00:00:00Z/files/reference.fasta`

### 15. Make a pull request!

See this [friendly youtube video](https://www.youtube.com/watch?v=rgbCcBNZcdQ&ab_channel=JakeVanderplas) for help. Thanks so much for supporting the scientific community!
