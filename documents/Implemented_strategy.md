# Strategy when setfit is implemented with PyTorch Lighining

We implemented a policy of inheriting as much of the original SetFit code as possible so that this code would still be available when the Setfit code was updated.

# Difficulties when transferring to PyTorch Lighning

Original training flow of SetFit can be separated by 2 parts:

1. Training Sentence Transformers (Full epoch)
2. Training whole model (Full epoch)

Therefore, it was difficult to evaluate the model on every epoch. See corresponding [issue](https://github.com/huggingface/setfit/issues/178).

However, in this repository, we divided each epoch like `Training Sentence Transformers (single epoch)`→`Training whole model (single epoch)` so that we can evaluate the model for each epoch.

If the weight of the model body changes during `training whole model (single epoch)`, the implementation will be different from the original paper. Therefore, we avoided the problem by devising the following

## If classification head is consisted from sklearn

During `Training whole model` , vector of sentence is generated by model body and stored in each training steps. Finally, when all vectors are calculated, only the classification head is trained.

Therefore, the weight of the model body does not change.

## If classification head is consisted from nn.Module (PyTorch)

After the `Training Sentence Transformers` of every epochs, the weights of model body are stored.

Then after training the whole model, the weights of model body are reset by loading the stored weight.