Swayam & Sayan's ACN Project
PCA runs: 75 scripts, 75 confusion matrices, one results sheet
==============================================================

Grid
----
    5 component counts (2, 4, 6, 8, 10)
  x 3 test sizes       (0.2, 0.4, 0.6)
  x 5 classifiers      (sheet entries 2, 6, 7, 9, 13)
  = 75 runs

Every file is named for its own run:

    Test_0.2_DR_PCA4_DT.py    the script
    Test_0.2_DR_PCA4_DT.jpg   its confusion matrix

Classifiers
-----------
    #2   Logistic Regression   LR
    #6   Decision Tree         DT
    #7   Random Forest         RF
    #9   XGBoost               XGB
    #13  k-Nearest Neighbors   KNN

Two entries from the requested list could not be used:

  #1  Linear Regression predicts a continuous number. It produces no class
      labels, so accuracy, precision, recall, F1 and a confusion matrix
      cannot be computed from it. #13 is used instead.

  #15 Support Vector Machine was dropped after being measured. The base code
      applies no scaling, so PCA returns components still on the raw feature
      scale. The RBF kernel degenerates on those values and a single fit ran
      past 49 minutes without finishing. #9 fits the same data in about 5
      seconds.

Each script is SGDC_60_40.py
---------------------------
Open any run script beside SGDC_60_40.py. The original lines are unchanged.
Six changes are marked in place:

    CHANGE 1  run from the project folder, so the original CSV names resolve
    CHANGE 2  stratified sample of 100,000 flows
    CHANGE 3  the test size for this run
    CHANGE 4  PCA, copied from PCA_LDA_KErnel_PCA_CODE.pdf, only
              n_components differs per run
    CHANGE 5  the classifier, in place of SGDClassifier
    CHANGE 6  save the .jpg and add one row to the results sheet

Why a sample
------------
The three CSVs hold 1,308,978 flows. PCA handles that easily, but k-nearest
neighbours compares every test flow with every training flow, so its cost
grows with the product of the two. The same 100,000-flow sample is used by
all 75 runs, so every result is comparable. At least 50 flows of each class
are kept, because the rare attacks are very small: Heartbleed has only 11
flows in the entire dataset.

Metrics
-------
Precision, recall and F1 are weighted averages, printed exactly as
SGDC_60_40.py prints them. A weighted average is dominated by the benign
majority, which is 83% of the data. A model can therefore score well here
and still miss a rare attack completely. The confusion matrix images show
where that happens: read the rows for Heartbleed, Infiltration and the three
Web Attack classes.

Folders
-------
    1 - Base Code
        SGDC_60_40.py, unchanged. Open any run script beside it to see
        exactly what was changed.

    2 - Scripts and Confusion Matrices
        Test size 0.2   25 scripts and their 25 .jpg images
        Test size 0.4   25 scripts and their 25 .jpg images
        Test size 0.6   25 scripts and their 25 .jpg images
        Each .jpg sits next to the script that produced it and carries the
        same name.

    3 - Results
        75_runs_pca.xlsx and the same rows as 75_runs_pca.csv

How to run
----------
Put this whole folder inside the folder that holds the three CSVs:

    Tuesday-WorkingHours.pcap_ISCX.csv
    Wednesday-workingHours.pcap_ISCX.csv
    Thursday-WorkingHours-Morning-WebAttacks.pcap_ISCX.csv

Then run any script on its own, from anywhere:

    python "2 - Scripts and Confusion Matrices/Test size 0.2/Test_0.2_DR_PCA4_DT.py"

CHANGE 1 in each script walks up the folders until it finds those CSVs, so
the depth of the folders does not matter.

It prints the confusion matrix, accuracy, precision, recall, F1 and the full
classification report, writes its .jpg next to itself, and adds its row to
results/75_runs_pca.csv in the folder holding the CSVs.

Results
-------
75_runs_pca.xlsx holds every result:

    "75 runs"                Test | DR | Clas | Acc | Pre | Rec | F1
    "full detail"            the same plus run time and script name
    "best per classifier"    the highest F1 setting for each classifier
    "components x classifier"  F1 averaged over the three test sizes

Mean F1 by component count:

              PCA2    PCA4    PCA6    PCA8   PCA10
    LR      0.8569  0.8549  0.8615  0.8617  0.8624
    DT      0.9640  0.9796  0.9840  0.9857  0.9865
    RF      0.9703  0.9841  0.9878  0.9890  0.9901
    KNN     0.9686  0.9769  0.9791  0.9791  0.9807
    XGB     0.9576  0.9761  0.9831  0.9850  0.9881

More components always help, and the gain is largest from 2 to 4. Random
Forest is the strongest at every setting; Logistic Regression is the weakest
by a wide margin and barely improves with more components, because the
classes are not linearly separable in this projection.

Best run for each classifier, all at test size 0.2 with 10 components:

    RF    Acc 0.9914   Pre 0.9914   Rec 0.9914   F1 0.9913
    XGB   Acc 0.9890   Pre 0.9890   Rec 0.9890   F1 0.9889
    DT    Acc 0.9880   Pre 0.9881   Rec 0.9880   F1 0.9881
    KNN   Acc 0.9837   Pre 0.9829   Rec 0.9837   F1 0.9832
    LR    Acc 0.8765   Pre 0.8611   Rec 0.8765   F1 0.8632
