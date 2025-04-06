# parameters used in this code 
Parameters Used in the Code
1. Training Parameters:

    Epochs: 200
    Batch Size: 64
    Verbose: 1
    Optimizer: Adam with a learning rate of 0.0001
    Loss Function: Categorical crossentropy

    Model Construction Parameters (passed to build_model):

        input_shape: (n_timesteps, d_model)
        n_timesteps comes from the training data (sequence length)
        d_model comes from the number of features per timestep (e.g., 12)
        head_size: 128
        num_heads: 12
        ff_dim: 24
        num_classes: Determined by trainy.shape[1] (number of output classes)
        num_transformer_blocks: 1
        mlp_units: [128] (defines a single Dense layer in the MLP part)
        dropout (for both transformer and initial dropout): 0.25
        mlp_dropout: 0.4

    Within the Custom Layer (Time2Vec):

        kernel_size: 1 (passed to Time2Vec)

        The layer initializes four weights:

            w0 and b0 with shape (1,)
            w with shape (input_dim, kernel_size)
            b with shape (kernel_size,)

2. 