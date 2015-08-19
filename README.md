# Attempts at running ENIGMA on our CAMH 1.5T sample

## Attempt 1: Using only the CAMH 1.5T FA maps

- Input dir: input/
- Output dir: output/
- Data: from /archive/data/DT_sample/{Controls,Schizoprehnics}/allFA/*_FA.nii.gz

1. From datman, ran: dm-proc-enigmadti.py $PWD/input/ $PWD/output/

## Attempt 2: Using original merged DTI images

- Input dir: input_dti/
- dtifit output dir: dtifit
- Data: from /archive/data/DT_sample/{Controls,Schizoprehnics}/allCS/MotionCorrected.nii.gz

1. Ran dtifit.sh from datman: 

  for i in input_dti/*.nii.gz; do 
    sub=$(basename $i .nii.gz); 
    mkdir -p dtifit/$sub; sge_batch dtifit.sh $i dtifit/$sub 0 0.3; 
  done 

