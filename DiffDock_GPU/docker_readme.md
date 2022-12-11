
GPU Instructions:
Navigate to the directory containing Dockerfile_GPU_2

run 
  ' docker build -t diffdock —rm -f Dockerfile_GPU .'
  
attach the container with a GPU such that you can interact with the terminal 

docker run --name kellyd73-diffdock-test-2 --gpus=all -it -p 5000:5000 --group-add video kellyd73-diffdock-test:latest

generates embeddings 

  ' python datasets/esm_embedding_preparation.py --protein_path data/SIRT3/pdb4bn4.pdb --out_file data/SIRT3/prepared.fasta '
  
  
sent env variables:

  ' HOME=esm/model_weights python esm/scripts/extract.py esm2_t33_650M_UR50D data/SIRT3/prepared.fasta data/SIRT3/esm2_output --repr_layers 33 --include per_tok '
  
inference:

'python -m inference --protein_path data/SIRT3/pdb4bn4.pdb --ligand data/SIRT3/5514.sdf --out_dir data/SIRT3/results --inference_steps 20 --samples_per_complex 40 --batch_size 10 --actual_steps 18 --no_final_step_noise'

CPU:

  ' docker build -f diffdock —rm -f Dockerfile_GPU . '
  'docker compose run --rm app'
  
  
  
  
