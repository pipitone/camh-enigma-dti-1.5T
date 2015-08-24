# Attempts at running ENIGMA on our CAMH 1.5T sample

## Attempt 1: Using only the CAMH 1.5T FA maps

- Input dir: input/
- Output dir: output/
- Data: from `/archive/data/DT_sample/{Controls,Schizoprehnics}/allFA/*_FA.nii.gz`

1. From datman, ran: dm-proc-enigmadti.py $PWD/input/ $PWD/output/

## Attempt 2: Using original merged DTI images

- Input dir: input_dti/
- dtifit output dir: dtifit
- Data: from /archive/data/DT_sample/{Controls,Schizoprehnics}/allCS/MotionCorrected.nii.gz

Ran dtifit.sh from datman: 

```
  for i in input_dti/*.nii.gz; do 
    sub=$(basename $i .nii.gz); 
    mkdir -p dtifit/$sub; sge_batch dtifit.sh $i dtifit/$sub 0 0.3; 
  done 
```

That fails because images don't have bvec/bvals nicely named.

So, now we go and remerge the DTI repetitions: 

```
  cat subjects | \
  for i in S001 S004 S007 S008 S009 S013 S014 S018 S021 S022 S029 S032 S034 \
     S035 S036 S037 S039 S040 S047 S049 S053 S054 S059 S061 S062 \
     S063 S064 S068 S075 S081 S090 S092 S094 S096 S107 S112; do 
    paste /archive/data/DT_sample/Schizophrenics/allDTI/$i/DTI_rep*/*.bvec > merged_dwis/${i}.bvec 
    paste /archive/data/DT_sample/Schizophrenics/allDTI/$i/DTI_rep*/*.bval > merged_dwis/${i}.bval
    echo fslmerge -t merged_dwis/${i}.nii.gz /archive/data/DT_sample/Schizophrenics/allDTI/$i/DTI_rep*/*.nii.gz
  done | parallel -j8 --tag

  parallel sge_batch dtifit.sh {.}.nii.gz dtifit/{/.} 0 0.3 ::: merged_dwis/*.bvec
```

And then run ENIGMA: 
```
  dm-proc-enigmadti.py /scratch/jp/TGH_ENIGMA/dtifit enigma_from_dtifit/ --calc-all
```

