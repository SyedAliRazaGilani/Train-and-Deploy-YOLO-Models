## Train and Deploy YOLO for candy detection and calorie estimation

This repository teaches you how to use **computer vision** with [Ultralytics YOLO](https://docs.ultralytics.com/) to **detect types of candy** in images or live video, then **derive calories (and sugar) for each kind** using a simple lookup keyed by the predicted class. That lets you **keep a running tally of sweets in view**—for example, summing estimated intake when several brands are scattered on a desk or in a bowl.

**What this is useful for:** a hands-on path from **labeling data → training → deployment**, with a concrete application—**dietary awareness and portion-style logging** (rough estimates from packaging data you supply, not medical analysis), **demos and coursework** on object detection, **portfolio projects** that combine vision with structured nutrition tables, or **comparing sugar and calories across mixes** of candy for curiosity or education. Treat outputs as **informative estimates**: accuracy depends on your model, lighting, and whether your nutrition table matches real serving sizes.

![Train and Deploy YOLO Models](doc/Readmeimg.png)

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/SyedAliRazaGilani/Train-and-Deploy-YOLO-Models/blob/main/Train_YOLO_Models.ipynb) · [Open in Colab (Train_YOLO_Models.ipynb)](https://colab.research.google.com/github/SyedAliRazaGilani/Train-and-Deploy-YOLO-Models/blob/main/Train_YOLO_Models.ipynb)

### Training a custom YOLO model for candy detection (and sorting by type)

The full walkthrough is in **[Train_YOLO_Models.ipynb](https://colab.research.google.com/github/SyedAliRazaGilani/Train-and-Deploy-YOLO-Models/blob/main/Train_YOLO_Models.ipynb)** ([open in Colab](https://colab.research.google.com/github/SyedAliRazaGilani/Train-and-Deploy-YOLO-Models/blob/main/Train_YOLO_Models.ipynb#scrollTo=1sUfcA8ZgR2t)). Below is the candy-focused summary.

**What you get:** a detector with one class per candy type (for example Skittles vs. Snickers). At inference time, each bounding box has a **class label**, so your app can **count, filter, or sort** detections by that label (for example separate piles or totals per brand).

**Images and labels**

- For a solid proof-of-concept, plan on the order of **~200** training images; include **other objects and varied backgrounds/lighting**, not only candy on a plain surface.
- Label each candy instance in **YOLO format** (tools such as [Label Studio](https://labelstud.io/) work well). Export should follow a layout the notebook expects: an **`images`** folder, a **`labels`** folder, and a **`classes.txt`** label map listing every candy class name.
- Zip the dataset as **`data.zip`** (same folder layout as in the notebook). The Colab notebook also offers a **sample candy image dataset** you can download to try the pipeline end-to-end before using your own photos.

**Prepare data in Colab**

- Upload **`data.zip`** (Colab upload or Google Drive), unzip it, then run the repo’s **[`utils/train_val_split.py`](utils/train_val_split.py)** (as in the notebook) to split roughly **90% train / 10% validation** into the structure Ultralytics expects (`train` / `validation`, each with `images` and `labels`).
- Auto-generate **`data.yaml`** from **`classes.txt`**, install **Ultralytics**, and train—for a smaller dataset (under ~200 images), the notebook suggests a starting point such as **`yolo11s.pt`**, **`epochs=60`**, **`imgsz=640`** (adjust if your dataset is larger or you need faster inference).

**After training:** best weights land under the run folder (e.g. `runs/detect/train/weights/best.pt`); zip/download from Colab and use them locally with **`yolo_detect.py`** or build a small app on top of your weights as described below.

### From candy detections to calories

The camera model does **not** read calories from pixels. After YOLO detects each piece of candy, every detection has a **class** (for example Skittles vs. Snickers). You keep a **lookup table**—calories and sugar **per piece** for each class your model was trained on—and for each detection you add that row’s values. **Total calories** (and sugar) in view are simply the **sum** over all current detections, one serving entry per detected piece. The [examples/candy_calorie_counter](examples/candy_calorie_counter) script follows this pattern with a `nutrition_info` dictionary keyed by class name, filled from the model’s labels each frame.
