👋 Sign Language Inference

Full inference pipeline for sign language machine translation.

<p align="center">
  <a href="https://github.com/sign/.github/blob/main/CONTRIBUTING.md">Contribution Guidelines</a>
  ·
  <a href="https://github.com/sign/inference/issues">Submit an Issue</a>
</p>

<p align="center">
  <a href="https://github.com/sign/translate/actions/workflows/lint.yml">
    <img src="https://github.com/sign/inference/actions/workflows/lint.yml/badge.svg" alt="Lint Status Badge" />
  </a>
  <a href="https://github.com/sign/translate/actions/workflows/test.yml">
    <img src="https://github.com/sign/inference/actions/workflows/test.yml/badge.svg" alt="Test Status Badge" />
  </a>
  <a href="https://github.com/sign/translate/blob/master/LICENSE.md">
    <img src="https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey.svg" alt="License: CC BY-NC-SA 4.0 Badge" />
  </a>
</p>

<hr>

## Installation

```bash
pip install git+https://github.com/sign/inference
```

## Signed-to-Spoken Language Translation Pipeline

Let's start with having a video file of a sign language sentence, word, or conversation.

```bash
curl https://media.spreadthesign.com/video/mp4/13/93875.mp4 --output sign.mp4
```

Next, we'll use `video_to_pose` to extract the human pose from the video.

```bash
pip install mediapipe # depends on mediapipe
video_to_pose -i sign.mp4 --format mediapipe -o sign.pose
```

Now let's create an ELAN file with sign and sentence segments:
(To demo this on a longer file, you can download a large pose file
from [here](https://nlp.biu.ac.il/~amit/datasets/poses/holistic/dgs_corpus/1413451-11105600-11163240_a.pose))

```bash
pip install pympi-ling # depends on pympi to create elan files
pose_to_segments -i sign.pose -o sign.eaf --video sign.mp4
```

<details>
  <summary>Next Steps (TODO)</summary>

After looking at the ELAN file, adjusting where needed, we'll transcribe every sign segment into HamNoSys or
SignWriting:

```bash
pose_to_text --notation=signwriting --pose=sign.pose --eaf=sign.eaf
```

After looking at the ELAN file again, fixing any mistakes, we finally translate each sentence segment into spoken
language text:

```bash
text_to_text --sign_language=us --spoken_language=en --eaf=sign.eaf
```

</details>

## Spoken-to-Sign Language Translation Pipeline

Let's start with having a spoken language word, or sentence - "Hello World".

<details>
  <summary>Next Steps (TODO)</summary>

First, we'll translate it into sign language text, in SignWriting format:

```bash
text_to_text --spoken_language=en --sign_language=us \
  --notation=signwriting --text="Hello World" > sign.txt
```

Next, we'll animate the sign language text into a pose sequence:

```bash
text_to_pose --notation=signwriting --text=$(cat sign.txt) --pose=sign.pose
```

</details>


Finally, we'll animate the pose sequence into a video:

```bash
pip install git+https://github.com/sign-language-processing/pose-to-video

# Using Pix2Pix
wget -O pix2pix.h5 "https://firebasestorage.googleapis.com/v0/b/sign-mt-assets/o/models%2Fgenerator%2Fmodel.h5?alt=media"
pose_to_video --type=pix2pix --model=pix2pix.h5 --pose=sign.pose --video=sign.mp4 --upscale
```

<details>
  <summary>Next Steps (TODO)</summary>

```bash
# OR Using StyleGAN3
pose_to_video --type=stylegan3 --pose=sign.pose --video=sign.mp4 --upscale
# OR Using Mixamo
pose_to_video --type=mixamo --pose=sign.pose --video=sign.mp4
```

</details>

### Cite

```bibtex
@misc{moryossef2024inference,
    title={Sign Language Inference: Full inference pipeline for sign language machine translation},
    author={Moryossef, Amit},
    howpublished={\url{https://github.com/sign/inference}},
    year={2024}
}
```
