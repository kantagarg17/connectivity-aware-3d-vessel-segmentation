# Proposal: evaluate continuity in small abdominal vessels

## Motivation

The current AVT experiment is a useful starting point, but its large aorta and proximal branches do not by themselves establish continuity performance on thin vessels. Large structures contribute many voxels to aggregate Dice, potentially obscuring missing or disconnected small branches. The [AVT dataset paper](https://pmc.ncbi.nlm.nih.gov/articles/PMC8760499/) describes variable visualization of branches and reports a median slice thickness of 5 mm for KiTS, 0.625 mm for RIDER and 3 mm for Dongyang. Resampling to 1 mm cannot recover detail absent from the acquisition. The repository reports smoke tests, not completed full-volume comparative results.

The next anatomical target should be the deep inferior epigastric artery (DIEA), its intramuscular course through the rectus abdominis, and its perforators through the anterior rectus sheath. Confirm the precise label extent for every dataset; not all DIEA labels include the complete perforator course.

## Candidate datasets

1. **UZ Brussel DIEP CTA cohort** — [Kapila et al.](https://site.oaecdn.com/articles/ais.2026.01): 504 DIEP-planning CTA scans, including 55 with manually annotated 3D perforator vascular anatomy. This is the closest match for rectus-sheath perforator continuity. The paper says data can be made available upon reasonable request; access and reuse terms must be confirmed with the authors.
2. **Tianjin DIEA CTA cohort** — [Su et al.](https://www.mdpi.com/1424-8220/26/21/6771): 4,446 annotated axial images from 31 patients, with bilateral DIEA labels from the external iliac origin to the anterior rectus sheath. Data are available from the corresponding author upon reasonable request and are not public. Confirm access to aligned original 3D volumes and masks: the study exported PNG slices and used image-level splits, which are inadequate evidence of patient-independent 3D continuity. Reconstruct or reannotate aligned 3D labels only if the necessary source data are available.
3. **Abdominal artery and vein CT dataset** — [Hrubovcak et al.](https://www.nature.com/articles/s41597-026-07303-2), [Zenodo](https://doi.org/10.5281/zenodo.17407158): 60 patients, including arterial masks in 50 arterial-phase scans, with mesenteric and other abdominal vessel branches. Public under CC BY 4.0. This is a practical abdominal small-branch benchmark, **not a verified rectus-sheath or DIEP ground truth**; the authors warn that vessels under 4 mm may be incompletely delineated. Check image coverage and commission new expert annotations before claiming DIEP performance.

## Proposed evaluation

- Separate patients across training, validation and held-out test sets; avoid splitting neighboring slices from the same patient. Preserve a separate external cohort if available.
- Record native slice thickness, in-plane spacing, contrast phase and quality; compare the current 1 mm preprocessing with a finer or anisotropic approach where supported by the native data. Fix image/label alignment and evaluate at a documented resolution.
- Obtain expert-reviewed 3D vessel masks or centerlines within a defined rectus and sheath region, including origin, branches and fascial exit points where visible. Mark uncertain or unannotated regions explicitly; do not automatically treat them as negative. Review a sample with a second expert.
- Compare the existing Dice + cross-entropy and Dice + weighted soft-clDice modes under matched data and training settings. Add Dice + cross-entropy + weighted clDice as an ablation to isolate the contribution of clDice. Select thresholds and checkpoints using validation data, with the same rule for each model.
- Report per-patient and per-branch results, stratified by annotated vessel diameter and image resolution: Dice, clDice, centerline recall and precision, branch detection, breaks per branch or length, and connectivity from the parent artery to annotated distal endpoints. Define matching tolerances and failure criteria before testing, and report false connections as well as breaks. Include patient-level paired uncertainty intervals.
- Inspect disagreements on the source CTA: a prediction outside an incomplete mask can be a real omitted vessel or a false positive. Do not score unlabeled anatomy as confirmed ground truth. Document visibility limits and annotation uncertainty.

This is a proposed study, not a claim of demonstrated small-vessel performance. The currently reported smoke tests do not answer the question.
