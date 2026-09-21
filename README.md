# 15. FAIR exercises
### Exercises in Interoperability and Reusability

## Part 1: Three ways to run BLAST & PlantGenIE API - Interoperability

### PlantGenIE: Gene information and expression 

- The output from the annotation retrieval for the gene Potra2n18c32336 via the PlantGenIE API was:
    
        {"results":[{"geneId":"Potra2n18c32336","geneName":null,"description":"Belongs to the glycosyltransferase 2 family. Plant cellulose synthase subfamily"}]}

        From this, I am able to understand that this gene belongs to the glycosyltransferase 2 family, which is a subfamily in the plant cellulose synthase family. Hence, the protein encoded should be functionally involved in producing plant cellulose in *Populus tremula*.

- geneName is null, while the other fields are populated. The resource leaving one metadata field empty while populating another tells me that the annotation was probably inferred from sequence similarity. If it was a curated annotation, the researcher in charge would have at least indicated that the gene has no name instead of leaving the GeneName field completely empty. 

- When retrieving the gene Potra2n4c9093, which we know has no description at all, with the same process, the output is the following: 

        {"results":[{"geneId":"Potra2n4c9093","geneName":null,"description":null}]}

        This gene has no annotation at all, only an individual geneID that names it. We can infer that the resource (the *Populus tremula* database on PlantGenIE) has no full curated annotation. We can also understand that it includes genes that have no complete annotation and were most probably annotated computationally via sequence similarity. 

- The input for the PlantGenIE API is a JSON request body, while NCBI/UniProt requires a single URL, either for searching for sequences in the database (esearch) and getting geneIDs or retrieving sequences (efetch). This tells me that the PlantGenIE API has both search and retrieve in the same API request, while the NCBI/UniProt API has this split up into two separate requests. 

## PlantGenIE: Gene expression

- There are 106 samples represented.

- The gene is most highly expressed in the SWD samples, which are the Secondary Cell Wall samples. That makes sense considering that one of the major components of the secondary cell wall in woody plants is cellulose. 

- The expression unit is 'vst'. 

- Given that I already know what data to use and have it, the remaing question to be answered are: Which tools were used, including the verion and parameters. In what environment were the tools run? Should I create a certain software environment?


### BLAST

#### website
- I actively set the following parameters: Core nucloetide database (core_nt), optimize for highly similar sequences. Algorithm parameters were left at default: 
Max target sequences: 100; Short queries: Automatically adjust parameters for short input sequences; Expect threshold: 0.05; Word size: 5; Max matches in a query range: 0; MMatrix: BLOSUM62; Gap Costs: Exostence: 11 Extion: 1; Compositional Adjustments: Conditional compositional score matrix adjustment

- The search took about 12s.

- Besides the list of hits with respective data (species, description, query vocer, % identity, e-value, accession number, etc, there are options to display a graphical summary of how where the hits align to the query sequence, an alignemnt option showing the hits aligned to the query, and a taxonomy option, showing which taxa have the highest hit rate. 

- It would take a really long time to only do the BLASTing manually individually for each query and even more time to then manually evaluate each single output.

#### local/HPC
- The setup effort might be longer and more complex in the beginning, but once the database and script alreay exist and one understand how it works it is adjustable very quickly. 

- The SLURM script gives me the possibility to do the blast via an external cluster and choose the number and types of cores, the maximum time the search should run and the option to directly get a predifened output file. Moreover, the should be extendable onto multiple sequences and therefore way more time-efficient.  

#### API
- I get back a file that can directly be used as input for another program, hence making it pipeline-friendly. However, the output files for EBI and PlantGenIE are strucutred differently, posing some doubts about interoperability. Nevertheless, having a prompt that gives a defined output that can be incorporated into a pipeline makes BLASTing via an API much more time-efficient and practical for big-scale analyses.

- USing defined API endpoints allows me to incorporate this part of the analysis into bigger program-pipelines, hence making it interoperable. Moreover, I can mention concretely which API andpoints where used, what they were set to and which version of the API was utilized in a report, which makes the method very reproducable.

- For the PlantGenie API the top five hits seem to be different "versions" of the query sequence (have different numbers after the dot in the ID). For the EBI blast, the top hit seems to be the query sequence, and the following hits are of the corresponding gene in other closely related species. This tells me that BLAST scores a perfect match by sequence similarity, which is influenced by percentage identity, coverage and the e-value. 

### Comparing the three results:
**Approach 	Speed 	                        Scalability 	            Reproducibility 	            Control**
Website 	Fast for 1 sequence 	        Not scalable 	            Low (no record of parameters) 	Limited
API 	    Moderate                        ~10s of sequences 	        Good (parameters in code) 	    Moderate
Local HPC 	Slow to set up, fast to run 	Highly scalable 	        Excellent (script + Slurm log) 	Full


## Interoperability assessment

[PASS, PARTIAL, FAIL were assesed looking at the metadata on ENA alone. In the written answer, the article was also taken into considertion in some cases]

**Finding the dataset**
- [PASS] Can you locate the dataset by searching the GEO or ENA database without knowing the accession in advance? 
        I was able to find the dataset in the ENA browser without knowing the accesion before, with the help of the paper title. The title as a whole did not work, but the key words "Populus tremula wood formation" gave me multiple hits, including the correct database.

- [PASS] Is there a persistent, stable identifier (accession number) for the dataset? 
        Yes

- [PASS] Is the identifier cited in the paper?
        The identifier is cited twice in the methods section, once under the RNA extraction paragraph and in a separate Accession number paragraph at the end of the methods section.


**Sample identification** 
- [PASS] Can you tell how many samples are in the dataset? 
        There are 137 samples in the dataset.

- [PASS] Can you assign each sample to an experimental condition (control vs treated, genotype A vs B, etc.) from the metadata alone? 
        It is possible to get a minimum amount of information about the sample conditions, specifically which tissue the sample was taken from, both from the 'Library Name' and the 'Sample title' in the metadata. 

- [PARTIAL] Are biological replicates identifiable?
        I think that biological replicates are identifiable: it should be the samples that have the same tissue type in their 'Lilbrary Name' / 'Sample title', but different Experiment Accessions. This give sme the impression that they come from different experiments, aka from different individuals. 


**Biological metadata**
- [PASS] Is the organism identified with a taxonomy ID (not just a name)? 
        The organism is identified with a tax ID.

- [PARTIAL] Is the tissue or cell type specified? Is it annotated with an ontology term (e.g. UBERON, EFO, CL)? 
        The tissue/cell type is specified in 'Sample title' and 'Library name'. No ontology term though.

- [PARTIAL] Is the treatment or experimental condition specified? Is it annotated with an ontology term (e.g. EFO, CHEBI)? 
        The experimental conditions do not seem to be mentioned in ontological terms. 

- [FAIL] Are any other relevant variables specified (age, sex, genotype, growth conditions, time point)?
        Other than the tissue type, there are no other relevant informations about experimental conditions are specified in the metadata on ENA. In the article, one can find that all of the individual were clones and naturally growing.


**Technical metadata**
- [PASS] Is the sequencing platform specified? 
        The sequencing platform specified is: Illumina HiSeq 2000

- [FAIL] Is the library preparation protocol specified (stranded/unstranded, poly-A/ribo-depleted)? 
        The library preparation protocol is not specified in the metadata on ENA. In the article, details on how the library was produced are included. 

- [PARTIAL] Is read length and paired/single end specified?
        It is specified that the reads are paired, but read length is not specified.


**Data availability**
- [PASS] Are raw FASTQ files available (not only processed count matrices)? 
        FastQ files are available.

- [FAIL] Are processed results available (count matrix, normalised data)? 
        Processed results are not available.

- [FAIL] Is the reference genome/transcriptome version specified? 
        The reference genome/transcriptome is not specified in the metadata in ENA, but in the article. 

- [FAIL] Is the alignment tool and version specified?
        The alignment tool and version are not specified in the metadata in ENA, but in the article..


**Reproducibility**
- [FAIL] Is analysis code available? 
        The  methods are described in the methods section, but the code itself is not. 

- [PASS] Are software versions specified for all tools used? 
        Yes

- [FAIL] Is there a workflow definition (Snakemake, Nextflow) or equivalent? 
        No

- [FAIL] Does the paper declare adherence to a community metadata standard (e.g. MINSEQE)?
        No


#### Checklist summary
| Category              | Pass | Partial | Fail |
|-----------------------|------|---------|------|
| Sample identification |   2  |    1    |      |
| Biological metadata   |   1  |    2    |   1  |
| Technical metadata    |   1  |    1    |   1  |
| Data availability     |   1  |         |   3  |
| Reproducibility       |   1  |         |   3  |

#### Key finding
    While the metadata about the samples is somewhat complete and therefore usable (especially after reading the methods section in the article), actually redoing the computational analysis would be challenging - even more so as a person with little to nue clue about how such analyses are done. The article does provide info on which methods where applied (and references to specific articles about them), but no source code is available. Therefore, I could not simply rerun the analysis, but would have to really get into understandying the underlying analytical concepts and fetch code from elsehwere.
