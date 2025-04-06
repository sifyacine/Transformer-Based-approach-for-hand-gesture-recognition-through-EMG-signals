  subject 28  original accuracy = 60.32
# accuracy = 56.75 (decrease by 4)
    model = build_model(
       input_shape=(n_timesteps, d_model),
       head_size=128,
       num_heads=1,
       num_classes=n_outputs,
       ff_dim=3,
       num_transformer_blocks=1,
       mlp_units=[128],
       mlp_dropout=0.4,
       dropout=0.25,
    )
    verbose, epochs, batch_size = 1, 100, 64
    decrease in time as well

# accuracy =  58.63
time = 6 min
same model parameters
verbose, epochs, batch_size = 1, 150, 128


# accuracy = 54.20

time = 6 min
same model parameters
verbose, epochs, batch_size = 1, 200, 256


# accuracy = 53.80

time = 8 min
same model parameters
verbose, epochs, batch_size = 1, 200, 512


# accuracy = 60.45

time = 9 min 34 seconds
same model parameters
verbose, epochs, batch_size = 1, 200, 128
