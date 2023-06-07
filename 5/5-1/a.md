{
 "cells": [
  {
   "cell_type": "code",
   "execution_count": 1,
   "metadata": {},
   "outputs": [
    {
     "name": "stderr",
     "output_type": "stream",
     "text": [
      "2023-06-07 00:51:13.235209: W tensorflow/stream_executor/platform/default/dso_loader.cc:64] Could not load dynamic library 'libcudart.so.11.0'; dlerror: libcudart.so.11.0: cannot open shared object file: No such file or directory\n",
      "2023-06-07 00:51:13.235239: I tensorflow/stream_executor/cuda/cudart_stub.cc:29] Ignore above cudart dlerror if you do not have a GPU set up on your machine.\n",
      "/opt/conda/envs/tf/lib/python3.8/site-packages/tensorflow_addons/utils/tfa_eol_msg.py:23: UserWarning: \n",
      "\n",
      "TensorFlow Addons (TFA) has ended development and introduction of new features.\n",
      "TFA has entered a minimal maintenance and release mode until a planned end of life in May 2024.\n",
      "Please modify downstream libraries to take dependencies from other repositories in our TensorFlow community (e.g. Keras, Keras-CV, and Keras-NLP). \n",
      "\n",
      "For more information see: https://github.com/tensorflow/addons/issues/2807 \n",
      "\n",
      "  warnings.warn(\n",
      "/opt/conda/envs/tf/lib/python3.8/site-packages/tensorflow_addons/utils/ensure_tf_install.py:53: UserWarning: Tensorflow Addons supports using Python ops for all Tensorflow versions above or equal to 2.10.0 and strictly below 2.13.0 (nightly versions are not supported). \n",
      " The versions of TensorFlow you are currently using is 2.8.4 and is not supported. \n",
      "Some things might work, some things might not.\n",
      "If you were to encounter a bug, do not file an issue.\n",
      "If you want to make sure you're using a tested and supported configuration, either change the TensorFlow version or the TensorFlow Addons's version. \n",
      "You can find the compatibility matrix in TensorFlow Addon's readme:\n",
      "https://github.com/tensorflow/addons\n",
      "  warnings.warn(\n",
      "/opt/conda/envs/tf/lib/python3.8/site-packages/tqdm/auto.py:21: TqdmWarning: IProgress not found. Please update jupyter and ipywidgets. See https://ipywidgets.readthedocs.io/en/stable/user_install.html\n",
      "  from .autonotebook import tqdm as notebook_tqdm\n"
     ]
    }
   ],
   "source": [
    "import os\n",
    "\n",
    "import numpy as np\n",
    "\n",
    "import tensorflow as tf\n",
    "assert tf.__version__.startswith('2')\n",
    "\n",
    "from tflite_model_maker import model_spec\n",
    "from tflite_model_maker import image_classifier\n",
    "from tflite_model_maker.config import ExportFormat\n",
    "from tflite_model_maker.config import QuantizationConfig\n",
    "from tflite_model_maker.image_classifier import DataLoader\n",
    "\n",
    "import matplotlib.pyplot as plt\n",
    "\n",
    "\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 2,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Collecting numpy==1.23\n",
      "  Downloading numpy-1.23.0-cp38-cp38-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (17.1 MB)\n",
      "\u001b[2K     \u001b[90m━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━\u001b[0m \u001b[32m17.1/17.1 MB\u001b[0m \u001b[31m53.2 MB/s\u001b[0m eta \u001b[36m0:00:00\u001b[0m00:01\u001b[0m00:01\u001b[0m\n",
      "\u001b[?25hInstalling collected packages: numpy\n",
      "  Attempting uninstall: numpy\n",
      "    Found existing installation: numpy 1.24.3\n",
      "    Uninstalling numpy-1.24.3:\n",
      "      Successfully uninstalled numpy-1.24.3\n",
      "Successfully installed numpy-1.23.0\n",
      "Note: you may need to restart the kernel to use updated packages.\n"
     ]
    }
   ],
   "source": [
    "pip install numpy==1.23"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 2,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Downloading data from https://storage.googleapis.com/download.tensorflow.org/example_images/flower_photos.tgz\n",
      "228818944/228813984 [==============================] - 11s 0us/step\n",
      "228827136/228813984 [==============================] - 11s 0us/step\n"
     ]
    }
   ],
   "source": [
    "image_path = tf.keras.utils.get_file(\n",
    "      'flower_photos.tgz',\n",
    "      'https://storage.googleapis.com/download.tensorflow.org/example_images/flower_photos.tgz',\n",
    "      extract=True)\n",
    "image_path = os.path.join(os.path.dirname(image_path), 'flower_photos')\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 3,
   "metadata": {},
   "outputs": [
    {
     "name": "stderr",
     "output_type": "stream",
     "text": [
      "2023-06-07 00:53:21.196071: W tensorflow/stream_executor/platform/default/dso_loader.cc:64] Could not load dynamic library 'libcuda.so.1'; dlerror: libcuda.so.1: cannot open shared object file: No such file or directory; LD_LIBRARY_PATH: /opt/conda/envs/tf/lib/python3.8/site-packages/cv2/../../lib64:\n",
      "2023-06-07 00:53:21.196106: W tensorflow/stream_executor/cuda/cuda_driver.cc:269] failed call to cuInit: UNKNOWN ERROR (303)\n",
      "2023-06-07 00:53:21.196126: I tensorflow/stream_executor/cuda/cuda_diagnostics.cc:156] kernel driver does not appear to be running on this host (codespaces-5d9270): /proc/driver/nvidia/version does not exist\n",
      "2023-06-07 00:53:21.214276: I tensorflow/core/platform/cpu_feature_guard.cc:151] This TensorFlow binary is optimized with oneAPI Deep Neural Network Library (oneDNN) to use the following CPU instructions in performance-critical operations:  AVX2 AVX512F FMA\n",
      "To enable them in other operations, rebuild TensorFlow with the appropriate compiler flags.\n"
     ]
    },
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "INFO:tensorflow:Load image with size: 3670, num_label: 5, labels: daisy, dandelion, roses, sunflowers, tulips.\n"
     ]
    }
   ],
   "source": [
    "data = DataLoader.from_folder(image_path)\n",
    "train_data, test_data = data.split(0.9)\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 4,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "INFO:tensorflow:Retraining the models...\n",
      "Model: \"sequential\"\n",
      "_________________________________________________________________\n",
      " Layer (type)                Output Shape              Param #   \n",
      "=================================================================\n",
      " hub_keras_layer_v1v2 (HubKe  (None, 1280)             3413024   \n",
      " rasLayerV1V2)                                                   \n",
      "                                                                 \n",
      " dropout (Dropout)           (None, 1280)              0         \n",
      "                                                                 \n",
      " dense (Dense)               (None, 5)                 6405      \n",
      "                                                                 \n",
      "=================================================================\n",
      "Total params: 3,419,429\n",
      "Trainable params: 6,405\n",
      "Non-trainable params: 3,413,024\n",
      "_________________________________________________________________\n",
      "None\n",
      "Epoch 1/5\n"
     ]
    },
    {
     "name": "stderr",
     "output_type": "stream",
     "text": [
      "2023-06-07 00:53:51.937079: W tensorflow/core/framework/cpu_allocator_impl.cc:82] Allocation of 51380224 exceeds 10% of free system memory.\n",
      "2023-06-07 00:53:52.239148: W tensorflow/core/framework/cpu_allocator_impl.cc:82] Allocation of 51380224 exceeds 10% of free system memory.\n",
      "2023-06-07 00:53:52.275011: W tensorflow/core/framework/cpu_allocator_impl.cc:82] Allocation of 51380224 exceeds 10% of free system memory.\n",
      "2023-06-07 00:53:52.298263: W tensorflow/core/framework/cpu_allocator_impl.cc:82] Allocation of 25690112 exceeds 10% of free system memory.\n",
      "2023-06-07 00:53:52.310056: W tensorflow/core/framework/cpu_allocator_impl.cc:82] Allocation of 154140672 exceeds 10% of free system memory.\n"
     ]
    },
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "103/103 [==============================] - 53s 490ms/step - loss: 0.8680 - accuracy: 0.7743\n",
      "Epoch 2/5\n",
      "103/103 [==============================] - 51s 491ms/step - loss: 0.6494 - accuracy: 0.8962\n",
      "Epoch 3/5\n",
      "103/103 [==============================] - 48s 464ms/step - loss: 0.6177 - accuracy: 0.9172\n",
      "Epoch 4/5\n",
      "103/103 [==============================] - 48s 468ms/step - loss: 0.6024 - accuracy: 0.9266\n",
      "Epoch 5/5\n",
      "103/103 [==============================] - 48s 464ms/step - loss: 0.5883 - accuracy: 0.9293\n"
     ]
    }
   ],
   "source": [
    "model = image_classifier.create(train_data)\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 5,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "12/12 [==============================] - 8s 447ms/step - loss: 0.6267 - accuracy: 0.8992\n"
     ]
    }
   ],
   "source": [
    "loss, accuracy = model.evaluate(test_data)\n"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 6,
   "metadata": {},
   "outputs": [
    {
     "name": "stderr",
     "output_type": "stream",
     "text": [
      "2023-06-07 00:59:19.402795: W tensorflow/python/util/util.cc:368] Sets are not currently considered sequences, but this may change in the future, so consider avoiding using them.\n"
     ]
    },
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "INFO:tensorflow:Assets written to: /tmp/tmp5pel651x/assets\n"
     ]
    },
    {
     "name": "stderr",
     "output_type": "stream",
     "text": [
      "INFO:tensorflow:Assets written to: /tmp/tmp5pel651x/assets\n",
      "2023-06-07 00:59:23.687021: I tensorflow/core/grappler/devices.cc:66] Number of eligible GPUs (core count >= 8, compute capability >= 0.0): 0\n",
      "2023-06-07 00:59:23.687161: I tensorflow/core/grappler/clusters/single_machine.cc:358] Starting new session\n",
      "2023-06-07 00:59:23.764909: I tensorflow/core/grappler/optimizers/meta_optimizer.cc:1164] Optimization results for grappler item: graph_to_optimize\n",
      "  function_optimizer: Graph size after: 913 nodes (656), 923 edges (664), time = 22.888ms.\n",
      "  function_optimizer: function_optimizer did nothing. time = 0.008ms.\n",
      "\n",
      "/opt/conda/envs/tf/lib/python3.8/site-packages/tensorflow/lite/python/convert.py:746: UserWarning: Statistics for quantized inputs were expected, but not specified; continuing anyway.\n",
      "  warnings.warn(\"Statistics for quantized inputs were expected, but not \"\n",
      "2023-06-07 00:59:24.925077: W tensorflow/compiler/mlir/lite/python/tf_tfl_flatbuffer_helpers.cc:357] Ignored output_format.\n",
      "2023-06-07 00:59:24.925125: W tensorflow/compiler/mlir/lite/python/tf_tfl_flatbuffer_helpers.cc:360] Ignored drop_control_dependency.\n"
     ]
    },
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "INFO:tensorflow:Label file is inside the TFLite model with metadata.\n"
     ]
    },
    {
     "name": "stderr",
     "output_type": "stream",
     "text": [
      "fully_quantize: 0, inference_type: 6, input_inference_type: 3, output_inference_type: 3\n",
      "INFO:tensorflow:Label file is inside the TFLite model with metadata.\n"
     ]
    },
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "INFO:tensorflow:Saving labels in /tmp/tmp6egr5h0t/labels.txt\n"
     ]
    },
    {
     "name": "stderr",
     "output_type": "stream",
     "text": [
      "INFO:tensorflow:Saving labels in /tmp/tmp6egr5h0t/labels.txt\n"
     ]
    },
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "INFO:tensorflow:TensorFlow Lite model exported successfully: ./model.tflite\n"
     ]
    },
    {
     "name": "stderr",
     "output_type": "stream",
     "text": [
      "INFO:tensorflow:TensorFlow Lite model exported successfully: ./model.tflite\n"
     ]
    }
   ],
   "source": [
    "model.export(export_dir='.')\n"
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "tf",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.8.16"
  },
  "orig_nbformat": 4
 },
 "nbformat": 4,
 "nbformat_minor": 2
}
