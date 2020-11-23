### PixelCNN

https://arxiv.org/pdf/1606.05328.pdf

A autoregressive network that learnt the relationship between pixels, then generate them one by one. It can learn all pixels' distribution in parallel using CNN. It is trained with masked images and kernels. It models the joint probability distribution x as product of element-wise conditional distributions for each element. It improves on the pioneer model (pixelRNN, RNN does not work well for >100 samples)

#### WaveNet 2016

> https://arxiv.org/pdf/1609.03499.pdf

From Google Brain Team

inspired by neural autoregressive generative models that can model complex distribution, based on PixelCNN, find that it can generate audio with high temporal resolution (at least 16,000 samples per second). operate on raw audio waveform, probabilistic approach.

- WaveNet is designed as a fully convolutional neural network, where the convolutional layers have various dilation factors that allow its receptive field to grow exponentially with depth and cover thousands of timesteps
- The output audio sounds more natural than the best existing TTS systems, reducing the gap with human performance by over 50%.

- A stack of conv layers without pooling. main ingredient is causal convolution, it does not have recurrent connection, faster, but requires more layers or large filter to increase receptive field. causal convolution is convolution with holes, skipping input values with a certain step, it has been used in various place e.g. signal processing and image segmentation. In the paper the dilation is doubled for every layer, e.g. 1,2,4,…, 512,(repeated)1,2,4,…,512. their network is pretty deep.
- They find that softmax distribution is better than mixture model such as mixture density network or mixture of conditional Gaussian scale mixtures they have to output 65565 possibilities, to make it tractable, they use ulaw companding, quantize it to 256 possible values, where $\mu=255, -1 \lt x_t \lt 1$.
- They use a gated activation unit as in gated pixelRNN $\tanh(W_{f,k}\star x)\odot\sigma(W_{g,k}\star x)$, where $\odot$ is element-wise multiplication, $\star$ is convolution operation, $k$ is the layer index, $\sigma$ is sigmoid function and $j,g$ denote the gate and filter. They observed that this is better than rectified linear activation function.
- They condition the model on other inputs in two ways: global conditioning and local conditioning, global conditioning is a single latent representation $h$ that influences the output distribution across all timesteps, for example a speaker embedding layer in TTS model. The activation function now becomes: $\tanh(W_{f,k}\star x + V^T_{f,k}h)\odot\sigma(W_{g,k}\star x + V^T_{g,k}h)$, where $V$ is a learnable linear projection and it is broadcasted over the time dimension. For local conditioning they up-sampling $h$ before multiplying with $V$ using a transposed convolution network, this works slightly better.
- They also suggested to use a small context stack that process a fixed length portion of the input audio, it can have pooling layer that runs at a lower frequency and is consistent with the intuition that less capacity is required to model temporal correlations at longer timescales.
- They found that enlarging the receptive field was crucial to obtain samples that sounded musical, they finds that even with receptive field that ranged several seconds, genre, instrumentation, volume and sound quality varies from second to second.
- ?use sin to mask layer instead of this dilation

### Music Generation Using Neural Network Report 2016

RNN that generate music usually train by a short sequence of notes then repeatedly sampling from the output distribution to obtain the next note. Such kind of models often fall into generating over repeating notes or random sequences that lack a consistent theme or structure(tempo, chord progressions, phrasings, melodies).

Another difficulty or interesting field of music generation is the evaluation of music quality since it is largely subjective and can vary widely for any particular piece of music. Without a definitive standard for qualitatively or quantitatively evaluating a piece of music, it is difficult to judge and quality of generated music. They tried to measure the quality of music by calculating the similarity between the generated music and the music set used to train the model. They apply a quantitative measure based off of kernel density estimation called Indirect Sampling Likelihood(ISL).

Our Melody-RNN model produces quite listenable monophonic piano music piece given a prime cue. However, it will fall into the over repeating rabbit hole as most RNN based models when generating a music piece longer than 16 seconds. For Biaxial-RNN, it gives the best rhythmic music composition since we restrict on time-invariant and note-invariant properties.

#### Melody-RNN

from google open source project magenta. It is a dual layer network, three types: LSTM, Lookback and Attention, attention gives best result.

#### Biaxial-RNN

Two axis: time and note axis to achieve note invariant

get stuck playing the same chords for some time

- time-invariant: generate infinitely
- note invariant: note can freely transpose up and down without affecting much, because they sound similar.

#### Music Generation by Deep Learning – Challenges and Directions

> https://arxiv.org/pdf/1712.04371.pdf

- They find deep learning reaches limit as generated content tends to mimic the training set without exhibiting true creativity.

- **Control**

  - Sampling: usually to pick valid solution from random generated samples (costly, no guaranteed to succeed).

  - Condition: feed the architecture with extra condition information using extra layer so that we can gain some degree of parameterized control.

  - **input manipulation**: match input properties with some target properties.

    - auto-encoder, hope that bottleneck layer capture some features of the data that we can manipulate


### Juke Box

- hop length = T / S, T = number of samples, S = number of discrete code that uses to represents the music

- 4 mins ~= 10millions, each position about 16 bits, 44.1khz CD quality

- encoding E that maps each h in x to e in latent space E and decoding to decode the e back to input space, thus VAE with discretization bottleneck
  <img src="D:\UOM\Projects\Notes\in progress\3rd_year_project.assets\image-20201121165417964.png" alt="image-20201121165417964" style="zoom: 80%;" />
  sg = stop gradient operation that passes zero gradient during back propagation

  - reconstruction loss L improves the decoder
  - cookbook L improves distance between h and e
  - commit is to prevent the encoding for fluctuation too much, and beta control the amount of contribution to this loss (?).

  train a hierarchy model with decreasing size of H using non-autogressive encoder-decoder with simple mean-squared loss

  in this paper they use 3 layers, each level they use causal 1-d dilated concolutions, interleaved with downsampling and upsampling 1-d convolutions to match different hop length. They train these encoders separately, because the bottlenecked top levels are experiencing collapse.

- VQVAEs are known to suffer from codebook collapse, where input are map to single or little embedding vectors, and they use random restarts: when the mean usage of code book fall below a threshold, randomly reset it to one of the encoder outputs from current batch (?).

- when using sample level reconstruction loss, the model tends to reconstruct low frequencies only, to capture mid-to-high frequencies, we add a spectral loss defined as:
  <img src="D:\UOM\Projects\Notes\in progress\3rd_year_project.assets\image-20201121173317089.png" alt="image-20201121173317089" style="zoom:80%;" />
  STFT = Short-time Fourier transform(?)

- The upsampler is transformer with 

  



## Miscellaneous

### Autoregressive Model

It refers to model that predict the next value from previous values.

### Variational Autoencoder

Tradition autoencoder may just produce a mapping table (maybe just memorization), it can be not generative at all. Variational autoencoder introduce a restriction in z, such that it is distributed in a latent space (normally 0-1) following a probability distribution function, this can better generate new data.

variational inference is a idea to model the approximation rather than the exact one.

#### Traditional Encoder

- Denoising of data, e.g. denoising images.
- Sparse reconstructions for dimensionality reduction.

Latent space is not continuous, it can only work on training data

#### KL-divergence

The Kullback–Leibler divergence (also called relative entropy) is a measure of how one probability distribution is different from a second, reference probability distribution. 

![image-20201024183035093](C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201024183035093.png)

In term of difference between cross-entropy, their relationship can be model as:

![image-20201030131936448](C:\Users\weilu\AppData\Roaming\Typora\typora-user-images\image-20201030131936448.png)

Where H(p) is the entropy and H(p,q) is the cross-entropy 



### Sound Font

**Sound Font** is a brand name that collectively refers to a file format and associated technology that uses [sample-based synthesis](https://en.wikipedia.org/wiki/Sample-based_synthesis) to play [MIDI](https://en.wikipedia.org/wiki/MIDI) files.

### Posterior Collapse - VAE

We say a posterior is collapsing, when signal from input xx to posterior parameters is either **too weak** or **too noisy**, and as a result, decoder starts ignoring zz samples drawn from the posterior qϕ(z|x)qϕ(z|x).



#### PHD STUDENT

yian.deng@manchester.ac.uk





- finished up some RNN papers, some of the best samples comes from multi-track generation
- moved on to disentangling VAE - VQ-VAE: vector quantized VAE that basically uses discrete 
- started on the openai papers
  - they encode information of vocal using VQ-VAE, but the result 
  - downsampling and upsampling process introduces discernable noise