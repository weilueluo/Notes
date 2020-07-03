#### wavenet 2016

> https://arxiv.org/pdf/1609.03499.pdf

inspired by neural autoregressive generative models that can model complex distribution, find that it can generate audio with high temporal resolution (at least 16,000 samples per second). operate on raw audio waveform, probabilistic approach. 

- A stack of conv layers without pooling. main ingredient is causal convolution, it does not have recurrent connection, faster, but requires more layers or large filter to increase receptive field. causal convolution is convolution with holes, skipping input values with a certain step, it has been used in various place e.g. signal processing and image segmentation. In the paper the dilation is doubled for every layer, e.g. 1,2,4,…, 512,(repeated)1,2,4,…,512. their network is pretty deep.
- They find that softmax distribution is better than mixture model such as mixture density network or mixture of conditional Gaussian scale mixtures  they have to output 65565 possibilities, to make it tractable, they use ulaw companding, quantize it to 256 possible values, where $\mu=255, -1 \lt x_t \lt 1$.
- They use a gated activation unit as in gated pixelRNN $\tanh(W_{f,k}\star x)\odot\sigma(W_{g,k}\star x)$, where $\odot$ is element-wise multiplication, $\star$ is convolution operation, $k$ is the layer index, $\sigma$ is sigmoid function and $j,g$ denote the gate and filter. They observed that this is better than rectified linear activation function.
- They condition the model on other inputs in two ways: global conditioning and local conditioning, global conditioning is a single latent representation $h$ that influences the output distribution across all timesteps, for example a speaker embedding layer in TTS model. The activation function now becomes: $\tanh(W_{f,k}\star x + V^T_{f,k}h)\odot\sigma(W_{g,k}\star x + V^T_{g,k}h)$, where $V$ is a learnable linear projection and it is broadcasted over the time dimension. For local conditioning they up-sampling $h$ before multiplying with $V$ using a transposed convolution network, this works slightly better.
- They also suggested to use a small context stack that process a fixed length portion of the input audio, it can have pooling layer that runs at a lower frequency and is consistent with the intuition that less capacity is required to model temporal correlations at longer timescales.
- They found that enlarging the receptive field was crucial to obtain samples that sounded musical, they finds that even with receptive field that ranged several seconds, genre, instrumentation, volume and sound quality varies from second to second.
- ?use sin to mask layer instead of this dilation

#### Music Generation by Deep Learning – Challenges and Directions

> https://arxiv.org/pdf/1712.04371.pdf

- They find deep learning reaches limit as generated content tends to mimic the training set without exhibiting true creativity.

- **Control**

  - Sampling: usually to pick valid solution from random generated samples (costly, no guaranteed to succeed).

  - Condition: feed the architecture with extra condition information using extra layer so that we can gain some degree of parameterized control.

  - **input manipulation**: match input properties with some target properties.

    - auto-encoder, hope that bottleneck layer capture some features of the data that we can manipulate

    

- 