# Bandcamp Deep Learning

A small project for classifying Bandcamp album covers by their genre.

# Installation

* Locally: 
    * Create virtual environment (assumed to be named `bandcamp-deep-learning` below)
    * [Ubuntu 14.04] Install packages from `requirements-apt.txt`
    * Install Python requirements from `requirements.txt`
    * Install [Lasagne](http://lasagne.readthedocs.org/en/latest/user/installation.html)

* Remotely (on Ubuntu 14.04):
    * Install Fabric
    * Run `fab -i <pem_file> -H <host> -u <user> deploy` to setup the virtual enviroment, and deploy the project to
      the server. The project directory will be `~/bandcamp-deep-learning` and the virtual environment will be named
      `bandcamp-deep-learning` (surprisingly).

# Data collection

Download the images to, e.g., `~/data/images` by running:

    $ python manage.py download_dataset_images --out-dir ~/data/images --dataset-links-tsv dataset-links.tsv
    
This may take a while. You can check progress by running:

    $ find ~/data/images -maxdepth 1 -printf "echo \`ls %p | wc -l\`'\t'%p\n" | bash

After the downloads finish, you should have 10 sub-directories under `~/data/images`, each representing a genre, with
1000 album covers in each sub-directory. These were chosen at random from common album tags, as crawled for
[Bandcamp Recommender](http://www.bcrecommender.com/). Bandcamp album tags are assigned by artists, and each album
can have multiple tags. The chosen albums match only one of the chosen tags/genres.

Then, create the training/validation/testing split by running:

    $ python manage.py collect_dataset_filenames ~/data/images ~/data

# Running experiments

Check command line help to run experiments:

    $ python manage.py run_experiment --help

For example, to run a multi-layer perceptron (assuming you created the dataset as above):

    $ python manage.py run_experiment --dataset-json ~/data/local-dataset-filenames.json --architecture-name mlp

See notebooks for some examples and experimental results.

## Using the GPU

Set up CUDA as described in one of the many manuals (I simply used the AWS AMI from
[Caffe](https://github.com/BVLC/caffe/wiki/Caffe-on-EC2-Ubuntu-14.04-Cuda-7)) and run experiments with additional
with the correct environment variables. For example:

    $ THEANO_FLAGS=device=gpu,floatX=float32 python manage.py run_experiment --dataset-json ~/data/local-dataset-filenames.json --architecture-name mlp

This should be much faster than running with the default settings.