# finetune_dolly


## Getting Started with Training

First of all, Add the `dolly` repo to Databricks. 
  - move your cursor to the left on Databricks to see Repo. Under Repos click Add Repo, enter `https://github.com/databrickslabs/dolly.git`, then click Create Repo. 

### V100 GPUs
 <ol>
   <li>To run on V100 instances with 32GB of GPU memory (ex: `p3dn.24xlarge`, note that you must select the GPU runtime first, and unselect "Use Photon", for these instance types to appear), then make the following changes to your added repo, Databricks automatically save them after you make changes:
  * Modify the deepspeed config file `config/ds_z3_bf16_config.json` to configure optimizer offload. Within the `"zero_optimization"` section, add:
    ```
    "offload_optimizer": {
      "device": "cpu",
      "pin_memory": true
    },
   ```
    - Modify `training/trainer.py` to disable `bf16` and enable `fp16` in `TrainingArguments`:
  ```
  ...
  fp16=True,
  bf16=False,
  ...
  ```
 <li>  Open the `train_dolly` notebook in the Repo (which is the `train_dolly.py` file in the Github `dolly` repo). Set the `num_gpus` widget in `train_dolly` to the number of GPUs in your instance, typically 8. There will be three popup window (widget) on the top of the screen when you are in `train_dolly` notebook. You want to fill in the number 8 in the num_gpus window. You don't need to set the `dbfs_output_root` and `local_training_root` widget because the code will detect if these are Null, then they are set by default to "dolly_training".
<li/>

 <li>
   Attach the notebook to your GPU cluster, that is, over the top right of your screen, select your cluster. 
<li/>
 <li>
 Run all cells.  When training finishes, the notebook will save the model under `/dbfs/dolly_training` by default. It takes around 2 minutes to download and install all the dependency before actually start training. 
 <li/>
 <li>
 In the cell that contains following code is where you actually initiate training:
  ```
  !deepspeed {num_gpus_flag} \
    --module training.trainer \
    --deepspeed {deepspeed_config} \
    --epochs 1 \
    --local-output-dir {local_output_dir} \
    --dbfs-output-dir {dbfs_output_dir} \
    --per-device-train-batch-size 8 \
    --per-device-eval-batch-size 8 \
    --lr 1e-5
  ```
  Don't forget that You can click the cell output to scroll inside the output cell. Here is the details of the output for the above code:
  - it first load the tokenizer and the model and takes around 5 minutes to download the "pytorch_model.bin"
  - After initilizing the optimizer, configs and hyperparamters are printed 
  <li/>
 <ol/>
  
 ### A100 GPUs

