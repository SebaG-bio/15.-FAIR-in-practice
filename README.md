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

## Comparing the three results:
**Approach 	Speed 	                        Scalability 	            Reproducibility 	            Control**
Website 	Fast for 1 sequence 	        Not scalable 	            Low (no record of parameters) 	Limited
API 	    Moderate                        ~10s of sequences 	        Good (parameters in code) 	    Moderate
Local HPC 	Slow to set up, fast to run 	Highly scalable 	        Excellent (script + Slurm log) 	Full
