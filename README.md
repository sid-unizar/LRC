# Lexical Relation Classification and graded Lexical Entailment
## "No clues, good clues: Out of context Lexical Relation Classification"
This github contains the datasets, scripts and notebooks needeed to reproduce the results in the submitted paper: *No clues, good clues: Out of context Lexical Relation Classification*. It contains:
- datasets: all datasets used in the paper. The folder `datasets/hyperlex-orig` contains the original Hyperlex dataset. We use a modified version of this dataset (explained in Appendix A of the paper), `datasets/hyperlex`. It also contains the SoTA results in the literature (`datasets/sotas_results_literature/`) for a better visualization of the results.
- scripts: needed scripts to run the experiments.
- notebooks: notebooks to launch the above scripts in Google Colab, and to collect the results. 

### **Section 5.1 in the paper: Run the experiments**
To run the experiments in Section 5.1, following scripts are needed:
- `script/lrc_train_evaluate.py`: to run Lexical Relation Classification experiments with templates T1-T4.
- `script/masked_lrc_train_evaluate.py`: to run Lexical Relation Classification experiments with templates TM1-TM3.
- `script/gradedLE_train_evaluate.py`: to run graded Lexical Entailment experiments with templates T1-T4.
- `script/masked_gradedLE_train_evaluate.py`: to run graded Lexical Entailment experiments with templates TM1-TM3.

The scripts can be easly launched in Google Colab using the notebooks:
- `notebooks/lrc_train_evaluate_launcher.ipynb`
- `notebooks/masked_lrc_train_evaluate_launcher.ipynb`
- `notebooks/gradedLE_train_evaluate_launcher.ipynb`
- `notebooks/masked_gradedLE_train_evaluate_launcher.ipynb`

You have usage examples of the scripts in the regarding notebooks.

The `lrc_train_evaluate.py` is used to train a pretrained language model for LRC with templates T1-T4. It is used a train/val dataset during training and a test dataset to predict the lexical relation among two words. An usage example with EVALution datset and templates T1 (train and test templates `' <W1> ' <SEP> ' <W2> '`):
```
$python /content/drive/MyDrive/scripts/lrc_train_evaluate.py \
	--train_templates "' <W1> ' <SEP> ' <W2> '"   \
	--test_templates "' <W1> ' <SEP> ' <W2> '"   \
	--model  "roberta-base" \
	--nepochs 10 \
	--dir_output_results "/content/drive/MyDrive/res/" \
	--batch_size 32 \
	--warm_up 0.1 \
	--nrepetitions 1 \
	--dataset "EVALution" \
	--date `date "+%D-%H:%M:%S"` \
	--train_file "/content/drive/MyDrive/datasets/EVALution/train.tsv" \
	--test_file "/content/drive/MyDrive/datasets/EVALution/test.tsv" \
	--val_file "/content/drive/MyDrive/datasets/EVALution/val.tsv"
```
In the above example, it is observed some of the hyperparameters used in our experiments (batch size, epoch, warm up). The rest of hyperparameters are contained in the script.
The script produces two results files:
- A csv file: it contains one line for each pair of words (source and target words) and the relation to predict in the test dataset. Each line is formed by: the predicted/real label relation id (number from $0$ to $K$) and the label relation name; tokenization of the source/target words and number of tokens; probability of each label; number of synsets of the source/target words; the entropy of the probability distribution of the labels. 
- A txt file: the file contains $3$ lines. The first line is the printed version of a python dictionary with the value of all script parameters; the second line is the initial run date; and the thrid, the printed classification report of the function `classification_report` in the `sklearn` package.

Similar rationale is applied for the rest of the python scripts.

### **Section 5.1 in the paper: Collect the results of the experiments**
Once the scripts are run, you get a set of results files like the ones in the `results` folder. To process these results files, it can be used the notebook `results_processing.ipynb` to collect all the results.

### **Section 5.2 in the paper: Error analysis**
We performed an error analysis of the results over the CogALexV and EVALution datasets. For this error analysis the metadata files are needed:
- For CogAlexV: `datasests/CogALexV/relata_metadata.txt` and `datasests/CogALexV/pairs_metadata.txt`.
- For EVALution: `datasests/EVALution/EVALUTION_RELATA.txt` and `datasests/EVALution/EVALUTION_RELATIONS.txt`.

These files contains the POS, domain, prototipically value needed to perform the error analysis. 

Use the notebook `error_analysis.ipynb`.

### **Section 5.3 in the paper: Embedding Projection Visualization**
To control del learning process of the network we visualize the model embeddings by layers and epochs. The script to retrieve the processed embeddings for the visualization is `embedding_visualization.ipynb`.

Inputs for the script are the desired checkpoints downloaded from `notebooks/lrc_train_evaluate_launcher.ipynb` and `notebooks/masked_lrc_train_evaluate_launcher.ipynb`. The outputs are: a `visualization_metadata.tsv` file and a `visualization_tensors.tsv` file. 

Due to RAM  limitations in Google Collab, there is a limited number of examples (400) one can process a a time. 

## **Summary of the results**

We report the results for RoBERTa large model trained with the different templates in the paper.

K&+N, BLESS, EVALution and ROOT9 results in terms of the weighted f1-score weighted by the support of the labels
 
|Model|K&+N|BLESS|EVALution|ROOT9|
|---|---|---|---|---|
|RoBERTa/T1|0.989|**0.954**|**0.764**|**0.936**
|RoBERTa/T2|0.989|**0.955**|**0.757**|**0.936**
|RoBERTa/T3|0.989|<ins>**0.956**</ins>|<ins>**0.771**</ins>|<ins>**0.937**</ins>
|RoBERTa/T4|0.312|0.133|0.087|**0.934**
|RoBERTa/TM1|0.988|0.947|**0.761**|**0.936**
|RoBERTa/TM2|0.988|0.946|**0.764**|**0.928**
|RoBERTa/TM3|0.985|**0.951**|**0.746**|**0.926**
|LexNET|0.985|0.893|0.600|0.813
|KEML|<ins>**0.993**</ins>|0.944|0.660|0.878
|SphereRE|0.990|0.938|0.620|0.861
|RelBERT|0.949|0.921|0.701|0.910

CogAlexV results in terms of the f1-score for all relations and the weighted f1-score by the support of the labels

|Model|ant|hyp|part|syn|all|
|--|--|--|--|--|--|
|RoBERTa/T1|**0.873**|**0.703**|**0.752**|**0.604**|**0.743**|
|RoBERTa/T2|**0.863**|**0.682**|**0.745**|0.584|**0.728**|
|RoBERTa/T3|<ins>**0.884**</ins>|**0.718**|**0.784**|<ins>**0.629**</ins>|<ins>**0.762**</ins>|
|RoBERTa/T4|0.237|0.004|0.165|0.085|0.119|
|RoBERTa/TM1|**0.880**|**0.709**|**0.773**|**0.599**|**0.750**|
|RoBERTa/TM2|**0.871**|<ins>**0.723**</ins>|<ins>**0.787**</ins>|**0.621**|**0.758**|
|RoBERTa/TM3|**0.871**|**0.718**|**0.787**|**0.616**|**0.756**|
|LexNET|0.425|0.526|0.493|0.297|0.445|
|SphereRE|0.479|0.538|0.539|0.286|0.471|
|KEML|0.492|0.547|0.652|0.292|0.500|
|RelBert|0.794|0.616|0.702|0.505|0.664|

Graded LE results over Hyperlex dataset in terms of the Spearman correlation for all pairs words and restricted to noun and verb pairs:

|Model|random-split|lexical-split|
|----|---|---|
|RoBERTa/T1|**0.741**/**0.753**/**0.584**|**0.755**/**0.788**/**0.532** |
|RoBERTa/T2|0.152/0.170/0.030|0.287/0.350/0.063|
|RoBERTa/T3|0.774/0.790/0.631|**0.669**/**0.690**/**0.516**|
|RoBERTa/TM1|<ins>**0.828**</ins>/<ins>**0.839**</ins>/<ins>**0.716**</ins>|**0.789**/<ins>**0.837**</ins>/**0.612**|
|RoBERTa/TM2|**0.749**/**0.761**/**0.646**|**0.654**/**0.705**/**0.417**|
|RoBERTa/TM3|**0.814**/**0.830**/**0.683**|<ins>**0.794**</ins>/**0.828**/<ins>**0.656**</ins>|
|LEAR|0.686/0.710/------|0.174/------/------ |
|SDNS|0.692/------/------|------/------/------|
|GLEN|0.520/------/------|0.481/------/------|
|POSTLE|0.686/------/------|------/0.600/------|
|LexSub|0.533/------/------|------/------/------|
|HF|0.690/------/------|------/------/----- |
