# DiffDock_GPU

This respository takes the majority of its source code from the main DiffDock repository, found [here](https://github.com/gcorso/DiffDock).
It was copied over here and not forked in order to enable some restructuring of the file system and inclusion of dockerfiles for CPU and NVidia GPU implementations.

This repository contains 4 dockerfiles that build slightly different versions of the container for different purposes:
- Dockerfile_CPU
- Dockerfile_GPU
- Dockerfile_GPU_1_13
- Dockerfile_Github

The Github file is only to handle the automatic github workflows and is not intended for use due to its augmentation of the file pathing. 

## How to Use:

The first step you should take it cloning the repository to a location on your local hardware by running:

  git clone https://github.com/kjwallace/DiffDock_GPU
 
Then navigate withing the DiffDock_GPU directory:

  cd DiffDock_GPU
  
When viewing files in your terminal, you should see 4 Dockerfiles and a Directory named DiffDock.

If you would like to pull the pre-compiled GPU container, it can be found by running:

  docker pull ghcr.io/kjwallace/diffdock_gpu:latest
  
and run using:

  docker run --name diffdock \
    --gpus=all \
    -it \
    -v ./DiffDock/data:root/data \
    ghcr.io/kjwallace/diffdock_gpu:latest
    
 This is assuming you have properly installed NVidia GPU drivers and made them available to your install of Docker Desktop.
    
  


### CPU 

The CPU container is only recomended if you have no access to a GPU and want to use your own hardware, as DiffDock is computationally complex and takes a long time to run on only CPU. 

While in the DiffDock_GPU directory,run the following to build the image:
  
    docker build -t diffdock --rm -f Dockerfile_CPU .
    
Then navigate into the DiffDock directory and attach the image:

  cd DiffDock 
  docker compose run --rm app
  
This will attach the container in interactive mode and allow for running inference via the command line.


### GPU 
Only works on Cuda.
Make sure you have your drivers installed.

If you have a propoerly configured NVidia GPU that operates on Cuda 11.6, the image can be build by running:

  docker build -t diffdock —rm -f Dockerfile_GPU .
  
If you would prefer to use pytorch version 1.13.0 instead of 1.12.1, run the following instead:

  docker build -t diffdock —rm -f Dockerfile_GPU_1_13 .
  
You can attach the container with a volume for saving the results to your local machine using:

    docker run --name diffdock \
    --gpus=all \
    -it --rm\
    -v ./DiffDock/data:root/data \
    diffdock
  


### Inference 

Inference steps follows the same instructions as per the original DiffDock repo from Corso et al. The container already contains the files and dependencies necessary to generate feature embeddings via ESM. Inference can also be run using a CSV file with the paths to the desired complexes included, but for this read me, I will go over how to perform inference on a single complex.

First to generate the proper embeddings run:

  python datasets/esm_embedding_preparation.py --protein_path {path/to/protein.pdb} --out_file {path/to/directory/prepared.fasta}
  
Then an environment variable needs to be set for the inference:

  HOME=esm/model_weights python esm/scripts/extract.py esm2_t33_650M_UR50D {path/to/prepared.fasta} {path/to/complex/directory}/esm2_output --repr_layers 33 --include per_tok
 
 
Then to perform the inference:

  'python -m inference --protein_path {path/to/protein/file} --ligand {path/to/ligand} --out_dir {path/to/desired/directory} --inference_steps 20 --samples_per_complex 40 --batch_size 10 --actual_steps 18 --no_final_step_noise
  
The options of inference_steps, samples_per_complex, batch_size and actual_steps can be set to integer values if you would like to reduce run time.
