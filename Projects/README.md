# Projects

## FinetuningLlama2

### The model
Used Pytorch and HuggingFace API to fine tune Meta's base model Llama 2.
Model used: Llama2 7B to work on Google Colab GPU.

Time for training with T4 GPU: ~1 hour
Time for training with V100 GPU (Google Colab Pro): ~30 minutes

Needed to ask for access to the model in [Meta's website](https://llama.meta.com/) or [HuggingFace](https://huggingface.co/meta-llama/Llama-2-7b-hf)

Used [Dataset](https://huggingface.co/datasets/nickrosh/Evol-Instruct-Code-80k-v1) with instructs to the model to creade a specific piece of code, and outputs the piece of code that performs that task.
![alt text](https://github.com/juliagontijo/self_study/blob/main/Projects/img/dataset_overview.png)

Applied Lora and 4bit quantization to optimize the gradient optimization.

Results were visualized in real-time on [Wandb](https://wandb.ai/site) interface (integrated in the code).

### Results 
At first, the eval lost was spikey and with bad loss.
![alt text](https://github.com/juliagontijo/self_study/blob/main/Projects/img/old_loss.png)


Was training on 5000 rows of the dataset, with batch size = 2 and learning rate = 3e-5.
Training was also really slow due to the amount of data.
Updates parameters and the best result I got was:
![alt text](https://github.com/juliagontijo/self_study/blob/main/Projects/img/train_loss.png)
![alt text](https://github.com/juliagontijo/self_study/blob/main/Projects/img/eval_loss.png)
![alt text](https://github.com/juliagontijo/self_study/blob/main/Projects/img/train_eval_loss.png)

Final parameters:
- 1000 rows: 800 train 200 test
- pad_token = unk_token
- batch size: 20
- added gradient_checkpointing_kwargs={"use_reentrant": False}
- learning rate = 2e-4
- max_seq_length = 100

### What I've learned
Throught the process I learned that:
- When prompting a base model, the max_length of the output generation influences on the time to get response back, and should account for the length of the question on it besides the space for the answer itself.
- Start with a little amount of data to train fast and get insight on whats going to happen when you work with more data. Don't go running a model on huge datasets at first, it's going to take forever and the results may be not good at all. You nee dto understand whats happening to make the parameters and specifications right before.
- Use an amount of the data to eval. The eval_loss can show us if the model is overfitting or underfitting. (if eval_loss increases and train_loss decreases, its probaly overfitted; if train_loss doesn't decrease, its probably underfitted).
- Adjusting batch_size is really important for the models performance. 2 is a really low batch_size, but too large batches can take up too many GPU space and it won't work (had only 1 GPU available). Also, batch_size is the number of samples used in one forward backward pass on the network.
- Decreasing learning rate may help with overfitting. The model may be going too fast and just memorizing it, not really learning.
- Tokens are different for each model.
- GPUs are really valiable (and compute units on Google Colab hahaha)
- Prompting engineering is good but at a certain point. If I ask the model for a code in a instruction - input - ouput manner, it can output something kinda okay (the code itself) but then it always mumbles random things related to it. Fine tuning is more robust. This beeing said, to check if a model is good or not at answering to some prompt, don't format the prompt in a instruction way or anything like that (you're just going to be helping the model and not checking if it actuallt can respond well)

### Work to improve
- Not just get first K rows of dataset: the quality of data is important for training. Maybe try to filter diverse set of examples. Also don't use question length as a filter, instead use the tokenize question length (that is what really is going to affect the model and training time)
- Check if the padding used impacted on the training time/outcome
- Try to fine tune oon another model to see if performs better (Mistral or GPT for example)
