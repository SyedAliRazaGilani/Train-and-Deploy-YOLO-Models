![Train and Deploy YOLO Models](doc/Readmeimg.png)

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/SyedAliRazaGilani/Train-and-Deploy-YOLO-Models/blob/main/Train_YOLO_Models.ipynb) · [Open in Colab (Train_YOLO_Models.ipynb)](https://colab.research.google.com/github/SyedAliRazaGilani/Train-and-Deploy-YOLO-Models/blob/main/Train_YOLO_Models.ipynb)

## Training a custom YOLO model for candy detection

The step-by-step guide lives in **[Train_YOLO_Models.ipynb](https://colab.research.google.com/github/SyedAliRazaGilani/Train-and-Deploy-YOLO-Models/blob/main/Train_YOLO_Models.ipynb)**—open it in [Google Colab](https://colab.research.google.com/github/SyedAliRazaGilani/Train-and-Deploy-YOLO-Models/blob/main/Train_YOLO_Models.ipynb#scrollTo=1sUfcA8ZgR2t) so you get a Linux environment, Python, and (if you enable it) a GPU for training.

That notebook uses [Ultralytics](https://docs.ultralytics.com/) to train a **custom object detection** model: you define classes for each candy type you care about (for example Skittles, Snickers, and other brands), and the model learns to draw a box around each piece and predict which class it is. When training finishes, you have a **`.pt` weights file** you can use for inference on new images or video.

### Gathering and labeling candy images

Before training, you need photos where candies appear and **YOLO-format labels** (one box per candy instance, with the correct class). A practical starting point for a proof-of-concept is on the order of **~200 images**. Pictures should not all look the same: include **different backgrounds and lighting**, and sometimes **other objects** in the frame so the model learns what “candy” means in real scenes.

You can take your own photos and label them, or start from a public dataset if one fits your classes. For labeling, tools like **[Label Studio](https://labelstud.io/)** are a good fit: you export a layout the notebook expects—typically an **`images`** folder, a **`labels`** folder with one `.txt` per image, and a **`classes.txt`** file listing every class name in order.

When everything is ready, pack the dataset in the folder structure shown in the notebook, then **zip it as `data.zip`**. The notebook also describes how to use a **sample candy image dataset** so you can run the full train pipeline once before building your own set.

### Uploading data and splitting train / validation

In Colab you move **`data.zip`** onto the machine—either upload it in the Files panel, or store it in **Google Drive**, mount the drive, and copy the zip into `/content` (handy if the zip is large). After unzip, you split images into **training** and **validation** sets. The notebook uses this repo’s **[`utils/train_val_split.py`](utils/train_val_split.py)** script to send about **90%** of samples to **train** and **10%** to **validation**, in the folder layout Ultralytics requires (each split has `images` and `labels` subfolders).

### Config and training

You install **Ultralytics** in the notebook (`pip install ultralytics`). The last setup step is a **`data.yaml`** file that points to your train/val paths and lists class names; the notebook builds that from **`classes.txt`**.

Training is run with the `yolo detect train` CLI. The notebook explains how to pick **model size** (for example `yolo11n` through `yolo11x`—larger is often more accurate but slower; **`yolo11s.pt`** is a reasonable default), **epochs** (for under ~200 images, **60** epochs is a suggested starting point; for more images, fewer epochs may be enough), and **image size** (often **640**; smaller sizes can be faster). You can also use **YOLOv8** or **YOLOv5** checkpoints instead of YOLO11 by swapping the `model=` argument.

Let training **finish completely**—the notebook notes that an optimizer step at the end matters for the exported weights. The best checkpoint is usually saved as something like **`runs/detect/train/weights/best.pt`**. The notebook’s later sections show how to **run prediction** on validation images to visually check boxes and labels, and how to **zip and download** the trained model for use outside Colab.
