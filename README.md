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

**After training:** best weights land under the run folder (e.g. `runs/detect/train/weights/best.pt`); zip/download from Colab and use them locally with **`yolo_detect.py`** or the **Candy Calorie Counter** example below, which maps detected classes to nutrition info—another way to use per-class results.

## YOLO Application Examples

This folder contains example code showing different applications you can build around YOLO models.

### Candy Calorie Counter

The [examples/candy_calorie_counter](examples/candy_calorie_counter) example uses a custom YOLO model that is trained to identify popular types of candy (Skittles, Snickers, and so on). When candy is placed in front of the camera, the application checks the number of calories and grams of sugar in each piece of candy and reports the total calories and sugar. It is a basic example of how to use detected object classes to look up information about each object.

### Using YOLO With Multiple Cameras

The [examples/multi_camera](examples/multi_camera) example shows an efficient way to run YOLO models on multiple camera streams using Python multiprocessing.

### Toggle Raspberry Pi GPIO — Smart Lamp

The [examples/toggle_pi_gpio](examples/toggle_pi_gpio) example shows how to set up a “smart lamp” that turns on when a person is detected within a certain area of the camera’s view. It is a useful starting point for toggling the Raspberry Pi’s GPIO pins with YOLO and Python, and for working with detected object coordinates and decisions based on where an object appears on the screen.
