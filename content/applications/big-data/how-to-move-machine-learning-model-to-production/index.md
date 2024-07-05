---
slug: "Guidelines for Transitioning Machine Learning Models to Production"
description: 'This tutorial demonstrates the integration of a pre-trained deep learning model into a production application by leveraging an API endpoint within a Flask framework'
keywords: ["deep learning", "big data", "python", "keras", "flask", "machine learning", "neural networks"]
og_description: 'Incorporate a pre-trained deep learning model within a production application.'
published: 2024-03-18
modified: 2024-03-18
modified_by:
  name: Utho
title: 'Transitioning Your Machine Learning Model to Production: A Step-by-Step Guide'
authors: ["Pawan Kumar"]
---

How to Move Your Machine Learning Model to Production

Creating, training, and refining a deep learning model for tasks like natural language processing (NLP) or image recognition demands considerable time and resources. Typically, this involves harnessing potent processors to train the model on extensive datasets. However, once the model performs effectively, making predictions on fresh data becomes easier and less computationally intensive. The primary challenge lies in transitioning the model from its development stage to a production application.

This guide simplifies the process by demonstrating how to develop a straightforward Flask API. This API will utilize machine learning to recognize handwritten digits, employing a basic deep learning model trained on the well-known MNIST dataset.

## Before You Begin

1. If you haven't already, set up a Utho account and create a Compute Instance.

1.  Refer to our guide for updating your system. Additionally, you might want to adjust the timezone, set up your hostname, establish a restricted user account, and enhance SSH access security.

The examples in this guide are based on Ubuntu 16.04. Adapt the commands accordingly for your distribution. The provided scripts are in Python 3, but they should function properly with Python 2 as well.

## Creating a Python Virtual Environment

To maintain isolation between creating and deploying your model using Python, it's advisable to utilize virtual environments. This ensures that any alterations made to your Python setup won't impact the broader system.

1.  Download and install Miniconda, a slimmed-down version of Anaconda. Follow the on-screen instructions in the terminal, granting Anaconda permission to append a PATH location to `.bashrc:`

        wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
        bash Miniconda3-latest-Linux-x86_64.sh
        source .bashrc

2.  Create and Activate a New Python Virtual Environment:

        conda create -n deeplearning python
        source activate deeplearning

Upon successful execution of these commands, your terminal prompt will now display `(deeplearning)` as a prefix.

3.  Install Dependencies within the Virtual Environment:

        conda install keras tensorflow h5py pillow flask numpy

## Prepare a Model

Training complex models on extensive datasets typically requires specialized machines equipped with powerful GPUs (Graphics Processing Units). However, to streamline the focus on the deployment process, this guide will swiftly construct a basic model using a manageable dataset. This approach enables quick training, even on a laptop or basic Utho server.

### MNIST Database

In the early stages of machine learning, training computers to recognize handwritten numbers was a significant task. Organizations like the U.S. Post Office employ such classifiers to automate information input and processing. One prominent dataset for this task is MNIST, comprising 70,000 images of handwritten digits. Each image, sized 28x28 pixels, depicts a single preprocessed and labeled digit. For more details, visit the [official site](http://yann.lecun.com/exdb/mnist/).

Various models, ranging from basic linear classifiers to intricate neural networks, have been trained on MNIST. Presently, the top-performing models achieve an impressive [error rate of only 0.21%](https://en.wikipedia.org/wiki/MNIST_database). In this guide, we'll utilize a simple CNN (Convolutional Neural Network) capable of achieving approximately 97% accuracy.

### Building a Deep Learning Model Using Keras

Keras serves as a Python-based deep learning library, offering an object-oriented interface compatible with various deep learning frameworks like Theano and TensorFlow.

Since developing and training a deep learning model is beyond the scope of this tutorial, the code below is provided without explanation. The model is a simplified version of the example from [Elite Data Science's excellent tutorial](https://elitedatascience.com/keras-tutorial-deep-learning-in-python). If you don't have a background in deep learning and are interested in learning more, you can complete that tutorial and then skip to the [Flask API](#flask-api) section of this guide.

As the focus of this tutorial doesn't extend to developing and training a deep learning model, the code provided below lacks explanation. It represents a simplified version of an example from [Elite Data Science's excellent tutorial](https://elitedatascience.com/keras-tutorial-deep-learning-in-python). If you're new to deep learning and wish to delve deeper, consider completing that tutorial before proceeding to the [Flask API](#flask-api) section in this guide.

{{< note respectIndent=false >}}

The simplicity of this model and the modest size of the dataset allow running the script either on a Utho or on your local machine. However, executing it on a machine without a GPU will still require a minimum of ten minutes. If you prefer to bypass this step, you can download a pre-trained model by running the command `wget`.

{{< /note >}}

For older versions of Keras, it's necessary to delete optimizer weights in the pre-trained model. If you download the pre-trained model from GitHub, the script below verifies and eliminates optimizer weights.

     {{< file "optimizer-weights.py" py >}}
     import h5py
     with h5py.File('my_model.h5', 'r+') as f:
     if 'optimizer_weights' in f.keys():
     del f['optimizer_weights']
     f.close()
     {{< /file >}}

1.  Make a directory for the model:

        mkdir ~/models && cd ~/models

2.  Generate a Python script to construct and train your model:

        {{< file "~/models/mnist_model.py" py >}}
        from keras.models import Sequential
        from keras.layers import Dense, Dropout, Activation, Flatten
        from keras.layers import Convolution2D, MaxPooling2D
        from keras.utils import np_utils
        from keras.datasets import mnist

        (X_train, y_train), (X_test, y_test) = mnist.load_data()
         X_train = X_train.reshape(X_train.shape[0],1,28,28)
         X_test  = X_test.reshape(X_test.shape[0],1,28,28)
         X_train = X_train.astype('float32')
         X_test = X_test.astype('float32')
         X_train /= 255
         X_test /= 255

        Y_train = np_utils.to_categorical(y_train, 10)
        Y_test = np_utils.to_categorical(y_test, 10)

        model = Sequential()

        model.add(Convolution2D(32,(3,3),activation='relu',input_shape=(1,28,28),dim_ordering='th'))
        model.add(MaxPooling2D(pool_size=(2,2)))
        model.add(Dropout(0.25))
        model.add(Flatten())
        model.add(Dense(128, activation='relu'))
        model.add(Dropout(0.5))
        model.add(Dense(10, activation='softmax'))
        model.compile(loss='categorical_crossentropy',
              optimizer='adam',
              metrics=['accuracy'])

        model.fit(X_train, Y_train,
        batch_size=32, nb_epoch=5, verbose=1)

        model.save('my_model.h5')

      {{< /file >}}


3.  Execute the script:

        python ./mnist_model.py

You might encounter a warning message like the one below during a pip or conda installation, indicating that installing from source could potentially provide better performance.

           {{< output >}}

TensorFlow library wasn't compiled to utilize SSE4.1 instructions, although your machine supports them, potentially enhancing CPU computations' speed.

          {{< /output >}}

If the script runs successfully, you'll find the `my_model.h5` file in the `models` directory. Using the `model.save()` command in Keras enables you to save both the model architecture and the trained weights.

## Flask API

After training a model, utilizing it for predictions becomes straightforward. This section focuses on creating a simple Python API using Flask. The API will feature a single endpoint: it will accept POST requests with an image attached. Subsequently, it will utilize the previously saved model to identify the handwritten digit within the image.

1.  Establish a directory for the Flask API:

        sudo mkdir -p /var/www/flaskapi/flaskapi && cd /var/www/flaskapi/flaskapi

2.  Move the pre-trained model to the root of the Flask app:

        sudo cp ~/models/my_model.h5 /var/www/flaskapi/flaskapi

3.  Open a text editor and create `/var/www/flaskapi/flaskapi/__init__.py`, then add the following code:

        {{< file "/var/www/flaskapi/flaskapi/__init__.py" py >}}
        from flask import Flask, jsonify, request
        import numpy as np
        import PIL
        from PIL import Image
        from keras.models import load_model

        app = Flask(__name__)

        model = load_model('/var/www/flaskapi/flaskapi/my_model.h5')

        @app.route('/predict', methods=["POST"])
        def predict_image():
        # Preprocess the image so that it matches the training input
        image = request.files['file']
        image = Image.open(image)
        image = np.asarray(image.resize((28,28)))
        image = image.reshape(1,1,28,28)

        # Use the loaded model to generate a prediction.
        pred = model.predict(image)

        # Prepare and send the response.
        digit = np.argmax(pred)
        prediction = {'digit':int(digit)}
        return jsonify(prediction)

        if __name__ == "__main__":
        app.run()

{{< /file >}}

This time, you only need to import the `load_model` module from Keras, which reads `my_model.h5` and loads the model along with its weights. Once the model is loaded, the `predict()` function generates a set of probabilities for each digit from 0 to 9, indicating the likelihood of the image containing each digit. The `argmax` function from the Numpy library is then used to identify the number with the highest probability, representing the digit that the model predicts as the most likely match.

The format of the inputs to the model must exactly match the images used during training. These images were 28x28 pixel grayscale images, represented as an array of floats with the shape (1, 28, 28) (for color images, the shape would have been (3, 28, 28)). Therefore, any image submitted to the model must be resized to this exact shape. This preprocessing can be done either on the client-side or server-side. However, for simplicity, the example API provided above handles the preprocessing.

## Install mod_wsgi
Apache modules are usually installed along with the system installation of Apache. However, you can install `mod_wsgi` within Python to ensure compatibility with the appropriate virtual environment.

1.  Install Apache and development headers:

        sudo apt install apache2-dev apache2

2.  Install mod_wsgi as a Python module for Apache:

        wget https://pypi.python.org/packages/aa/43/f851abaad631aee69206e29cebf9f8bf0ddb9c22dbd6e583f1f8f44e6d43/mod_wsgi-4.5.20.tar.gz
        tar -xvf mod_wsgi-4.5.20.tar.gz
        cd mod_wsgi-4.5.20
        python setup.py install

3.  Utilize mod_wsgi-express to locate the installation path:

        mod_wsgi-express module-config

The output will resemble:

          LoadModule wsgi_module "/home/Utho/miniconda3/envs/deeplearning/lib/python3.6/site-packages/mod_wsgi-4.5.20-py3.6-linux-x86_64.egg/mod_wsgi/server/mod_wsgi-py36.cpython-36m-x86_64-linux-gnu.so"
          WSGIPythonHome "/home/Utho/miniconda3/envs/deeplearning"

4.  Create a file named `wsgi.load` in the Apache `mods-available` directory. Copy the `LoadModule` directive provided above and paste it into the file.

        {{< file "/etc/apache2/mods-available/wsgi.load" >}}
        LoadModule wsgi_module "/home/Utho/miniconda3/envs/deeplearning/lib/python3.6/site-packages/mod_wsgi-4.5.20-py3.6-linux-x86_64.egg/mod_wsgi/server/mod_wsgi-py36.cpython-36m-x86_64-linux-gnu.so"
        {{< /file >}}

5.  Enable the mod:

        a2enmod wsgi

## Configuring Virtual Hosting for Flask API

1.  Generate a `flaskapi.wsgi` file containing settings for your app:

        {{< file "/var/www/flaskapi/flaskapi/flaskapi.wsgi" >}}

#!/usr/bin/python

          import sys
          sys.path.insert(0,"/var/www/flaskapi/")

Import the app from flaskapi as application:

{{< /file >}}

2.  Set up a virtual host for your app by creating a file named `flaskapi.conf` in Apache's `sites-available` directory. Then, add the following content, replacing `example.com` with your Utho's public IP address. For the `WSGIDaemonProcess` directive, ensure to set the Python home path to the output of `mod_wsgi-express module-config` under `WSGIPythonHome`:

        {{< file "/etc/apache2/sites-available/flaskapi.conf" apache >}}
        <Directory /var/www/flaskapi/flaskapi>
        Require all granted
        </Directory>
        <VirtualHost *:80>
        ServerName example.com
        ServerAdmin admin@example.com
        WSGIDaemonProcess flaskapi python-home=/home/Utho/miniconda3/envs/deeplearning
        WSGIScriptAlias / /var/www/flaskapi/flaskapi/flaskapi.wsgi
        ErrorLog /var/www/html/example.com/logs/error.log
        CustomLog /var/www/html/example.com/logs/access.log combined
        </VirtualHost>

{{< /file >}}

3.  Establish a logs directory:

        sudo mkdir -p /var/www/html/example.com/logs

4.  Enable the new site and restart Apache:

        sudo a2dissite 000-default.conf
        sudo a2ensite flaskapi.conf
        sudo systemctl restart apache2

## Test the API

Your API endpoint is now prepared to receive POST requests with an attached image. In theory, the API should be capable of identifying any image containing an isolated handwritten digit. However, to ensure accurate predictions, it's essential to replicate the preprocessing steps used by the MNIST researchers on every image submitted to the model. This involves calculating the center of pixel density to center the digit within the image, as well as applying anti-aliasing.

To swiftly test the API, you can use `curl` to submit an image from the MNIST test set.

1.  Right-click and download the image below to your local machine:

2.  From your local machine, utilize `curl` to POST the image to your API. Ensure to replace the IP address with the public IP address of your Utho, and provide the absolute path to the downloaded image instead of `/path/to/7.png`:

        curl -F 'file=@/path/to/7.png' 192.0.2.0/predict

If successful, you'll receive a JSON response accurately identifying the digit in the image:

          { 'digit' : 7 }

The initial request might seem to take a bit longer because `mod_wsgi` adopts lazy loading of the Flask application.

## Next Steps

Many production machine learning solutions involve a more extensive pipeline than demonstrated in this guide. For instance, you could incorporate a different endpoint featuring a deep learning classifier for identifying digits in larger images. Each identified digit would then be forwarded to the `/predict` endpoint, allowing your application to interpret a series of handwritten digits, such as a phone number.

Moreover, the API outlined in this guide lacks several features crucial for a real-world application, such as error handling and managing bulk image requests. To enhance the service's utility, it's necessary to apply the complete preprocessing utilized by MNIST to each image.

Furthermore, images submitted to the API could serve as a data source for further training and refining your model. In this scenario, you could configure the API to store each submitted image, alongside the model's prediction, in a database for subsequent analysis.
