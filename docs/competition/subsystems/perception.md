# Perception: search, detection and mapping

**Subsystem owner:** Vision lead.

**Status:** Partial, v1. The dataset and training strategy is complete and well-sourced. The camera and lens trade, the Jetson throughput numbers, the image-to-ground geolocation error budget and the mosaicking comparison are outstanding — see section 6.

Two scored jobs: **Risk Mapping, 150 points** — a stitched aerial map handed to judges on a USB drive before the mission clock stops, late scores zero. And **Search, Detect and Deliver, 200 points** — autonomously find a mannequin and a tent and compute their coordinates so the aircraft can deliver within 50 ft.

**Both were scored by fewer than a third of the 2026 field** (8 of 27 for delivery, 10 of 27 for mapping — see [rules watch](../rules-watch.md)). That is where the differentiation is.

---

## 1. The failure mode is the pose prior, not the pixel count

This is the central finding, and it reverses the obvious priority.

**COCO — what every off-the-shelf detector is pretrained on — is 84.53% standing people.** The handbook says the mannequin "may be positioned in any orientation (laying down, sitting up, face-down, etc.)" and "will be surrounded/covered by surrounding features such as bushes/trees/vehicles."

Published measurement on exactly this problem: "it is impractical to directly use detectors pre-trained on standard datasets, especially in unusual scenarios where we need to detect people in uncommon positions, such as search and rescue," and for prone targets "the detection accuracy of the pre-trained models was very low."

**So the thing most likely to beat us is a face-down mannequin half under a bush, not a shortage of pixels.** Synthetic data fixes precisely that: leave-one-pose-out experiments show prone performance collapses without prone-specific training data and recovers with it.

**Second failure mode, also measured: target-layout confusers.** Pretrained models performed *worse* on a mannequin dataset than on a real-person one because the mannequin set "contains some other objects, such as **traffic cones and fiducials, which are easily misclassified as humans** when captured by the pre-trained detectors at high altitudes." The handbook's "scattered amongst other debris" is the same hazard, deliberately.

**Third: geometry.** Near-nadir viewing is the **best** geometry for geolocation and the **worst** for a COCO-pretrained detector — "the detection results of all three models are unsatisfactory for images that are captured with a high viewing angle (approaches the nadir view)… This may be partly the result of the training dataset not containing nadir or near-nadir views," and "the detection accuracy increases for a given altitude as the camera is moved farther away… because the pre-trained detection model uses ground imagery." **The resolution is not to abandon nadir. It is that this is a training-data problem, and it is the specific thing our own data-collection flight must fix.** Collect at the pitch we will actually fly.

---

## 2. The dataset that is almost exactly our problem

**Archangel** (Shen et al., *IEEE Access* 11, 2023, `doi:10.1109/access.2023.3299235`, preprint `arXiv:2209.00128`) is a UAV human-detection benchmark **built with mannequins**, for the same reason we would use one — test-facility safety rules and "the difficulty of asking humans to maintain certain strenuous poses, such as prone and squat, for a long period of time."

| Subset | Targets | Images | Altitude |
| --- | --- | ---: | --- |
| **Archangel-Mannequin** | **mannequins and vehicles** | **178,800** | **15–50 m in 5 m steps** |
| Archangel-Real | real people | 41,400 | 15–50 m |
| Archangel-Synthetic | 8 virtual characters | 4,423,700 | 5–80 m |

All three poses throughout: **stand, squat/kneel, prone.** Mannequin set captured at 45° pitch.

**They answer the mannequin-to-real transfer question directly, and the answer has a sting.** Fine-tuning on a joint set of a **2% subset** of the mannequin data plus balanced synthetic raised AP50 **by about 30 points** on real humans. But: "the models fine-tuned on **too much** data from Arch-Mann-FT37 tended to perform **worse** on Arch-Real-Eval… the models started to learn certain dataset-specific features from the fine-tuning dataset, adversely affecting the models' generalization capability."

**More proxy data is actively harmful past a point.** Plan a small, deliberate flight, not an exhaustive one.

**Train a small model.** Their best configuration was **YOLOv5n6**; the s6 and m6 variants "significantly decreased their performance on stand and kneel" because the synthetic set "might be too simple for larger models." Small model at high resolution, not a large model at 640.

**Open item, and the highest-value single action in this document: email the authors for the dataset.** No public download URL could be found.

Supporting result from man-overboard imagery with targets at 5 to 50 px: **97 real training images gave mAP 0.77; 90 synthetic images gave mAP 0.79** on the same real test set — "nearly identical object detection performance was achieved using real and synthetic data sets."

---

## 3. Copy-paste is the engine, and harmonisation is a trap

Three papers measured this, and the results are counterintuitive enough to be worth the table.

**Blending, from "Cut, Paste and Learn" (`arXiv:1708.01642`), mAP@0.5:**

| Blending | mAP |
| --- | ---: |
| None | 65.9 |
| Gaussian blurring | 68.9 |
| **Poisson blending** | **58.4** |
| All modes, randomised | 72.4 |
| **All modes, same scene rendered every way** | **73.7** |

**Poisson blending — the "correct" harmonisation choice — was 7.5 AP worse than doing nothing.** The win comes from randomising blending so the detector learns to ignore it: "synthesizing the same scene with… the blending factor changes makes the training algorithm invariant to these blending factors and improves performance by 8 AP points over not using any form of blending." A second paper confirms it: "we also found that **simply composing without any blending has similar performance.**"

**Augmentation ablation from the same work, baseline 73.7:** no 2D rotation 69.7, no 3D rotation 68.3, no truncation 71.8, **no occlusion 63.1**, all plus distractors **76.2**. Occlusion and truncation are worth as much as 10 AP, synthesised at max IoU 0.75 with at least 25% of the box in frame. **That is the mechanism that covers "surrounded/covered by bushes/trees/vehicles," and it is the largest single term in the paper.**

**And the cross-dataset result, which is our actual situation** since competition day is a domain we have never trained on:

| Training set | mAP on an unseen domain |
| --- | ---: |
| 100% real | 41.9 |
| Synthetic only | 36.5 |
| **10% real plus synthetic** | **43.2 — beats 100% real** |
| 100% real plus synthetic | 51.1 |

**Two traps, both measured.** First, from small-object work (`arXiv:1902.07296`): replacing the originals with pasted versions scored **worse than baseline** (det AP_s 0.161 against 0.167), while keeping both scored 0.179. The diagnosis: "the trained model effectively overfit to 'pasted' small objects but not necessarily to the original small objects… due to the artifacts from pasting." Evaluating that model on a *pasted* validation set showed a spurious **+38%**. **Always keep the un-pasted originals, and never validate on pasted images.**

Second, from the same paper: "it is crucial **not to introduce any overlap** when copy-pasting a small object, and that it is **not advisable to Gaussian-blur the edge** of a pasted object." That is not a contradiction with the occlusion finding — synthesise occlusion *by design* at a controlled IoU; do not let random pastes collide accidentally.

**Oversampling images containing small objects at 3× is best** (det AP_s 0.167 to 0.177) — **but large-object AP falls monotonically**, 0.418 to 0.398. It is an explicit trade, and for us the small objects are the entire task.

**Copy-pasting instances we already have adds nothing.** From aerial work: "vanilla Copy-Paste augmentation drags down the overall mAP since there are no new instances involved, while our Copy-Paste method on synthetic helipad instances shows obvious gain on this specific hard-case class." For a class with 91 training images, that gain was **+30.3 mAP**. **The gain lives in pasting instances from outside the aerial set — which is by definition our mannequin and tent chips.**

---

## 4. Do not build a simulator

**Synthetic-only aerial detection is about 10 mAP@50 — effectively useless.** Measured across VisDrone, SeaDronesSee and a cattle dataset with a game-engine pipeline at 50 to 100k images.

Two results from that work should set priorities:

| Change | Synthetic-only gain |
| --- | ---: |
| **Doubling in-game render fidelity** | **+0.1 mAP** |
| **Aligning the capture geometry** (altitude, viewing angle, time) while discarding 90% of the images | **+7.4 mAP** |

**Render fidelity bought nothing. Geometry alignment bought everything.** The gap that matters is "the imaging conditions at the time of capture, such as altitude, viewing angle and time."

And the tooling does not exist as assumed. **Three verified negatives:**

- **NVIDIA Isaac Sim and Omniverse Replicator publish no AP numbers** for synthetic detection data. Both public detection case studies report image counts and validate qualitatively. **Do not plan around NVIDIA case-study numbers; they do not exist publicly.**
- **No maintained Blender-based aerial dataset generator exists.** BlenderProc is the real pipeline but its tool paper carries no AP numbers; aerial-specific repositories were single-author with near-zero stars. **"Use the existing Blender aerial pipeline" is not an option — we would be building it.**
- **No AirSim-generated aerial detection dataset with published AP.** AirSim and its fork are alive, but the quantified game-engine aerial work is elsewhere.

**And there is no public SUAS dataset or generator for the current target spec.** Cornell's `cuair/perception` repository is **two commits, both from March 2021, zero stars** — a weekend prototype generating alphanumeric shapes and colours for the **retired** AUVSI target spec, with no training code, no dataset and no evaluation. The target class it was built for no longer exists.

**Copy-paste onto real backgrounds is the whole technique.**

---

## 5. Scale matching, and the one number to compute first

**Matching the mean pixel size of your targets is counterproductive. Matching the full distribution is worth +3.2 to +3.9 AP.** From Scale Match (`arXiv:1912.10664`):

| Pretrain | MR_tiny50 (lower better) | AP_tiny50 |
| --- | ---: | ---: |
| ImageNet | 87.78 | 43.55 |
| COCO as-is | 86.58 | 43.38 |
| **COCO100 — naive resize so the mean matches** | 87.67 | **43.03, worse than plain COCO** |
| Scale Match — full distribution transformed | 86.30 | 46.77 |
| **Monotone Scale Match** | **85.71** | **47.29** |

Monotone Scale Match exists because plain scale matching "may make the original size out of order: a very small object may be transformed to a bigger one than a large object." **That gain is larger than most architectural changes in the same table.**

**So the first thing to compute is the pixel-size distribution our camera and lens produce at 150 to 250 ft, and paste at exactly that distribution, monotonically.** Combined with the geometry-alignment result in section 4, the instruction is unambiguous.

A separate sweep found real data "can be reduced by up to 70% without sacrificing detection performance," with best real ratios of 5 to 20% — and crucially, the saving was far larger for the person-like class (538 real images matched an all-real model's 2,727) than for cars, "because the real world dataset already contains a lot of car instances." **Our mannequin and tent are the person-like case. Synthetic buys us the most.**

---

## 6. The playbook, and what is outstanding

### Do this

1. **Email the Archangel authors for the dataset.** 178,800 mannequin images at 15 to 50 m in stand, squat and prone, plus 4.4 M synthetic at 5 to 80 m. Nothing else has a better ratio of value to effort.
2. **Pretrain on HERIDAL plus Archangel plus NOMAD plus SARD** — HERIDAL for the aerial search geometry, Archangel for the mannequin and the prone pose, NOMAD for per-distance ground sample distance, SARD for volume.
3. **Fly one deliberate data-collection sortie.** A real mannequin and a real pop-up tent cost under $200 together. Fly **150, 200 and 250 ft at the nadir-ish pitch we will actually fly**, in several sun angles, with the mannequin standing, sitting, lying, face-down and half under a bush — and **scatter traffic cones, buckets and bags as confusers.** **A few hundred frames, not thousands**, because more proxy data measurably hurts real transfer.
4. **Copy-paste as the main engine.** Target chips onto our own aerial backgrounds at the **monotone-scale-matched** pixel-size distribution. **Randomise lighting direction and fabric texture aggressively** — fixed lighting cost 6.1 AP and no texture variation 4.7 AP in the published ablation, and over 1,000 textures was the threshold in another. **Synthesise occlusion deliberately** at max IoU 0.75 with 25% of the box in frame. **Do not harmonise** — render each scene under several blending modes instead, and never Poisson-blend. **Never paste with accidental overlap.**
5. **Keep un-pasted originals at about 2:1, oversample target-containing frames about 3×, and never validate on pasted imagery.**
6. **Train a small model at high resolution.** YOLOv5n6-class, not a large model at 640.
7. **Build no simulator.**
8. **Expect the prone mannequin to be the failure mode.** If we test one thing before competition, **put a face-down mannequin half under a bush and fly over it at 200 ft at our real camera pitch.** Half a day. It tests the one failure mode the literature says will beat us, and it tells us whether our training distribution is wrong before we have built anything around it.

### Outstanding

Research was stopped partway to conserve session budget. Still needed:

- **Camera and lens trade with the ground-sample-distance and motion-blur arithmetic worked out.** At 22 to 25 m/s, what shutter speed holds blur under one pixel? Candidate sensors, lenses, interfaces and prices.
- **Jetson Orin Nano Super measured throughput**, and whether 8 GB becomes the binding constraint when stitching concurrently, compared honestly against Orin NX 16 GB.
- **Image-to-ground geolocation error budget in metres** — the effect of attitude error, altitude source, time synchronisation and boresight calibration, and how much multi-observation averaging helps. [Payload delivery](payload-delivery.md) currently assumes 2 m for this term; that assumption is unverified and it is worth 1.4 points of hit probability if halved.
- **Mosaicking options with measured runtimes**, and which can finish a competition-sized area inside the clock. Known: OpenDroneMap is not viable — the only measured run was 46 images in 19m50s on a laptop and 2h31m on a Jetson Nano, CPU-bound. OpenREALM is the open-source starting point.
- **Whether onboard detection beats downlinking imagery to a ground laptop with a real GPU**, and whether a gimbal is necessary at all.

### Contested

One researcher cited a NASA low-flying-UAS dataset with a test dummy at `ntrs.nasa.gov/api/citations/20240007462`; another could not find it in NTRS at all. **Verify the URL directly before relying on it.**
