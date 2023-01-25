# Lexical Relation Classification and graded Lexical Entailment
## "No clues, good clues: Out of context Lexical Relation Classification"
This github contains the datasets, scripts and notebooks needeed to reproduce the results in the submitted paper: *No clues, good clues: Out of context Lexical Relation Classification*. It contains:
- datasets: all datasets used in the paper. The folder `datasets/hyperlex-orig` contains the original Hyperlex dataset. We use a modified version of this dataset (explained in Appendix A of the paper), `datasets/hyperlex`. It also contains the SoTA results in the literature (`datasets/sotas_results_literature/`) for a better visualization of the results.
- scripts: needed scripts to run the experiments.
- notebooks: notebooks to launch the above scripts in Google Colab, and to collect the results. 

### **Section 5.1 in the paper: Run the experiments**
To run the experiments in Section 5.1, it is needed the scripts:
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
We performed an error analysis of the results over the CogALexV and EVALution datasets. For this error analysis are needed the metadata files:
- For CogAlexV: `datasests/CogALexV/relata_metadata.txt` and `datasests/CogALexV/pairs_metadata.txt`.
- For EVALution: `datasests/EVALution/EVALUTION_RELATA.txt` and `datasests/EVALution/EVALUTION_RELATIONS.txt`.

These files contains the POS, domain, prototipically value needed to perform the error analysis. 

Use the notebook `error_analysis.ipynb`.
