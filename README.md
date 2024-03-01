# Abricate-PAI-DB
Using Abricate to detect phages from PAI database
# Rationale
The idea behind creating this database was to detect the presence of phages within our isolates of *Staphylococcus aureus* using the tool abricate. As abricate does not perform phage detection, we decided to create a database containing specific phage sequences from Pathogenicity Island Database ([PAIDB](http://www.paidb.re.kr/)) and used this database to detect phages present in our samples. The following SOP has been written to try and help anyone else trying to create a custom database like we did. For *S. aureus* phage related studies, the database we created can be used and by changing just a few details, one can successfully create their own phage database for any bacteria using the data from PAIDB and run it on abricate.

## Creating custom database in abricate:

# Create Environment
conda create -n abricate -c bioconda

# Activate the environment
conda activate abricate

# Install abricate
conda install -c conda-forge -c bioconda -c defaults abricate
# Navigate to Abricate Database Folder
cd /path/to/abricate/db

# Create New Database Folder
mkdir SAPAI

# Edit Fasta Files
# Download fasta files from PAIDB and ensure they adhere to the following format:
>DB~~~ID~~~ACC~~~RESISTANCES DESC
# In our case, the required format was:
>SAPAI~~~ID~~~gene~~~phage_name
# Modify the sequences to match the specified format.

# The first line of the original fasta file looks like so:

>lcl|NC_003923.1_cds_WP_000361524.1_1 [locus_tag=MW_RS01930] [protein=Abi family protein] [protein_id=WP_000361524.1] [location=416307..417413] [gbkey=CDS]

# Next, we use the command below to edit the text file, rename it with “accession_phage_name_new.fasta”, storing it in a folder called “new” to achieve the above mentioned format:

sed 's/lcl|NC_003923.1_cds_/SAPAI~~~/' NC_003923_P1_vSaα.txt | sed 's/\[gene=//' |sed 's/\[locus_tag=//' | sed 's/]//' | sed 's/ /~~~/' | sed 's/ /~~~vSa_alpha /' > new/NC_003923_P1_vSa_alpha_new.fasta

#**Note:** For phages with Greek alphabets (e.g., α) in their names, it's recommended to spell them out (e.g., alpha) while renaming the files and replace the alphabets with their spellings within the text file to avoid errors in the future.

# The first line of the modified fasta file should look like so:

>SAPAI~~~WP_000361524.1_1~~~MW_RS01930~~~vSa_alpha [protein=Abi family protein] [protein_id=WP_000361524.1] [location=416307..417413] [gbkey=CDS]


# Concatenate Fasta Files
```bash

cat *.fasta > sequences

# To remove the one line gap after concatenation between headers, you can use the following command:
sed -i '/^$/d' sequences

# Copy Sequences into the db folder 
cp sequences /path/to/abricate/db/SAPAI

# Setup and Run the Database
# Activate abricate
conda activate abricate

# Setup the database
abricate --setupdb

# Verify Database Addition
abricate --list

# Lastly, run your samples using the database you created
abricate --db SAPAI sample.fasta

