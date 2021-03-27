# Music Generation

## Introduction

Music generation is a multidisciplinary field that tries to achieve computational creativity. It can be dated back to late 1960s where a French music composer named Lannis Xenakis [@luqueStochasticSynthesisIannis2009] tries to use probability distributions to generate music through manipulate digital samples, but there were not much progress made for decades, because of the theoretical issue with computational creativity:

> If a machine can do only what it was programmed to do, how can its behaviour ever be called *creative*? [@ComputationalCreativity2021]

But in recent years, the ease of access to more computational power give raise to deep learning, a technique that has shown some promising signs in various specialised domains [@senguptaReviewDeepLearning2019]. Due to its black box nature (behaviour is not explicitly programmed), we hope that it can demonstrate creativity to some extent. Therefore, Artificial intelligence has been an active research area, lately, there are numerous software proposed to automate the creation and composition of music to a certain degree. In 2016, Google released Magenta [@47078], which built smart tools and interfaces based on new deep learning and reinforcement learning algorithms that extend music producers' process. In 2020, OpenAI released JukeBox [@dhariwalJukeboxGenerativeModel2020], a neural net that generate raw audio music that can sing along the given lyrics. Although there is still limited creativity in SOTA work, we are closer to the goal every year. In this project, I aimed to explore some of the proposed techniques, including dilated convolutions and auto-encoders, investigate the limitations and challenges on multi-track generation with no chord restriction.

## Background

### Music Composition

Let's establish some common ground before diving into the past works. In practical environment, music are usually stored in raw audio formats which are convenient for end-user (e.g. wav, mp3, flac, etc...), but it may not be the best format for people to conduct research as it is often too large to perform experiment. Therefore in the field of music generation, you often see another format, called [midi](https://www.midi.org/specifications/file-format-specifications/standard-midi-files) [@MIDIAssociationStandard]. In simple term, it stores when and which instrument is being play at what time and momentum, instead of storing the sound that they produce, allows researcher to conduct studies at higher level of abstraction. In this report, unless specifically stated, the input format for any model is midi.

I will be introducing some of the notable work that researchers have done in the past, to have a brief overview of the history and trends of music generation in recent year.

### Previous Works

In this section, I will have a brief overview of each state-of-the-art (SOTA) model in the past, discuss what problems they tried to solved, challenges and limitations that they had, and how each of them inspired the experiment I have done.

#### Melody RNN - 2016

Melody RNN [@GeneratingLongTermStructure] is a project in Google back in 2016 that attempts to explore the possibility of generating sequences such as melodies with long-term structure in midi events. One thing to keep in mind is that there were no transformer (but there is attention mechanism) and LSTM was still pretty new back then. The presented experiments primarily focus on melody generation, which means it is effectively a classification task of monotonic chords/notes.

There are two types of models available: Lookback RNN and Attention RNN, both of these models are built upon the Basic RNN model. Basic RNN is simply a one-hot vector of the previous event and predict the following event using LSTM cells.

**Lookback RNN** is similar to Basic RNN, except additional information and label are added to the input vector.

- Events from 1 to 2 bars before the previous bar are added, allows the model to recognize pattern more easily.
- Information of whether the previous event is repeating is added, this signal allows the model to recognise repetitive state more easily.
- Time signature of 4/4 are also added as binary step clock. 
    - step 1: `[-1,-1,-1,-1, 1]`.
    - step 2: `[-1,-1,-1, 1,-1]`.
    - step 3:  `[-1,-1,-1, 1, 1]`.
    - step 4:  `[-1,-1, 1,-1,-1]`.
    
    Time signature[^time_signature] is a notation used in music theory to specify how many beats (notes) are contained in each measure (bar). The reason for using 5 values instead of 3 values is not disclosed, but it does not matter since model can easily learn to ignore them.

**Attention RNN** uses the attention mechanism to learn even longer term structure, it works by allowing the model to learn the way to condition on previous steps. Note that the attention mechanism used in this model is different from what we known today, specifically:

$$
\begin{aligned}
u^t_i&=v^T\tanh(W'_1h_i+W'_2c_t)\\
a^t_i&=\text{softmax}(u^t_i)\\
h'_t&=\sum^{t-1}_{i=t-n}a^t_ih_i
\end{aligned}
$$

where the vector $v$ and matrices $W'_1$ and $W'_2$ are learnable parameters, and $h_i$ and $c_i$ are the output of the current RNN hidden states and cell states respectively. The $u_i$ represents how much attention each previous step should receive and softmax is used to normalise the values. An attention mask is also created to weight each of the previous step. The $h'_t$ is then concatenated with the RNN cell output follow by a linear layer and create the final output of the RNN cell, feeding to the next RNN cell.

Even though both models are limited to monophonic piano music, often fall into a repeating hole and not very rhythmic, Melody RNN is able to generate music more consistent throughout a long generated sequences than most previous attempts. With some editing, some simple tracks can be made. You can find generated samples and more information in their blog post[^melody_rnn].

[^time_signature]: https://en.wikipedia.org/wiki/Time_signature
[^melody_rnn]: https://magenta.tensorflow.org/2016/07/15/lookback-rnn-attention-rnn


#### WaveNet - 2016

WaveNet [@oordWaveNetGenerativeModel2016], WaveNet is a paper that brought many attention back in the days, because it is one of the earliest model that operate on raw audio while achieve SOTA in the field of text-to-speech audio generation, before WaveNet, researches are often not done on raw audio, since the number of samples in raw audio is too large for the available computational resource. Although it was not very effective in music generation from today's point of view, its technique has inspired many music generation works even up until today.

##### Architecture

WaveNet is based on PixelCNN [@oordConditionalImageGeneration2016], which is a work explores conditional image generation, WaveNet predict the next sample similar to PixelCNN models the joint probability distribution of each pixel $i$ over an image $x$:

$$
p(\bold{x}|\bold{h}) = \prod^{n^2}_{i=1}p(x_i|x_1,\dots,x_{i-1}, \bold{h})
$$

where $h$ represents the prior information, $x_i,\dots,x_{i-1}$ denotes the pixels before $x_i$, and $x_i$ represents the predicting pixels, and $n$ denotes the length of the image (assuming the image is square).

WaveNet applied this idea and uses the dilated causal convolution. The term *causal* simply refers to the assumption that all data points before a specified window is not relevant, as convolution layers required a consistent input size and does not use up all historical data. The term *dilated* refers to a mode of operation in the convolution layer where its filter is applied over an area larger than its specified kernel size by skipping on the input values:

![Visualization of a stack of dilated causal convolutional layers[^wavenet_paper]](6ddf4c1d84d45e403afb443df806aeef.png)

Since the dilated convolutions doubles for each added layer, this means the receptive field grow exponentially, allowing the network to have larger capability which is crucial for generating musical audio, and stacking these blocks further increases the capacity and receptive field size.

WaveNet also adapted the *gated convolutional layers* used in PixelCNN, that is, replacing rectified linear activation units between masked convolutions with following *gated activation unit*. Gated activation unit is a technique that used in RNN model to control what kind of information is allowed to let through, the multiplicative operations allows the model to model more complex interaction and avoid vanishing gradient, and this technique is found to be better than ReLU in this case.

$$
y=\tanh(W_{k,f}\ast \bold{x})\odot\sigma(W_{g,k}\ast \bold{x})
$$

where $k$ denotes the layer, $f$ and $g$ denotes the filter and gate, $\sigma$ denotes the sigmoid non-linearity, $\odot$ denotes the element-wise multiplication, $\ast$ denotes convolution operation, $W$ denotes the learnable parameter, $s$ denote $s=m(h)$ where $m$ is a function that map $h$ to a spatial representation, and $V$ is an $1\times 1$ convolution. Note that this is effectively same as the original gated activation unit used in PixelCNN, except that it does not have the hidden information $h$.

The residual connections (or skip connections) [@heDeepResidualLearning2015] is also applied in the WaveNet, residual connection is a mechanism that first applied in image recognition task, which won the ImageNet 2015 [@deng2009imagenet] challenge. Residual connection allows gradient to flow through the network directly without passing through the activation, it has the benefit of reducing the chance of gradient explode/vanish due to nature of non-linearity of the activation. Some people argue that skip-layer connections should not improve on the model performance, because it is just a linear term which the model could model it easily. But the real benefit of residual connection is it provides an alternating path for gradient, since the long chain of multiplications is the root cause of gradient explode/vanish.

![Residual learning: a building block[^residual_connection]](0770a29095c1a24dc3928e75f13dba19.png)

[^residual_connection]: https://openaccess.thecvf.com/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf


Lastly, each raw audio sample are often stored in 16-bit representation, this means that the model has to predict one of 65,565 classes which is huge, to make things more tractable, they also applied *$\mu$-law encoding* [@brokishALawMuLawCompanding] to the samples and quantise them to 8 bits. $\mu$-law encoding is a technique originally used in digital communication, it reduces the dynamic range of the audio signal and allow audio to be transmitted with increased signal-to-noise ratio (SNR) back in the days where there is not much bandwidth.

![Overview of the residual block and the entire architecture[^wavenet_paper]](e0a60cec57ebac99f9c38cc935623a9e.png)

Their experiment result shows that it is able to produce result that has preference score higher than the best baseline from LSTM-based and HMM-driven synthesizers in both English and Chinese music.

![Subjective preference score of samples from best baseline of LSTM-RNN-based statistical parametric and HMM-driven unit selection concatenative baseline synthesizers, and WaveNet that conditioned on both linguistic features and logarithmic fundamental frequency[^wavenet_paper]](adf0c5f658274d894af236d9822ec2f8.png)

[^wavenet_paper]: https://arxiv.org/pdf/1609.03499.pdf


Although quantitative evaluation of these models are difficult, but a subjective evaluation was conducted by listening to the samples produced. The researchers found that the large receptive field was crucial for it to sound musical, but the samples were not able to enforce long-term consistency and coherence. Nevertheless, the samples produced by the model were often harmonic and aesthetically pleasing.

To conclude, WaveNet presents a deep autoregressive generative model that combine causal and dilated convolutions to model long ranged temporal dependency, and showed promising results when applied to music generation, but they are not coherent enough to form a piece of music. If you are interested, you can find more information in their blog[^wavenet_blog].

[^wavenet_blog]: https://deepmind.com/blog/article/wavenet-generative-model-raw-audio

#### Midi Net - 2017

Many models for music generation use RNN, but since the success of Wave Net, there were also attempts to explore the possibility of convolutional network. One of the model that produced notable result is Midi Net, it attempts to generate melody using GAN architecture, where both generator and discriminator are convolutional network.

The representation of the music is represented by a $h$-by-$w$ matrix, where $h$ denotes the number of notes and $w$ denotes the number of time steps, and each entry $x\in \{0,1\}^{h\times w}$ denotes whether the note at the particular time step has been turned on (omitting volume).

The core of Midi Net is deep convolutional generative adversarial network (DCGAN), it trains two networks, the generator $G$ which attempts to generate fake samples with random input (noise) to "fool" the discriminator $D$, and $D$ will learn to distinguish real and fake samples, by challenging each other, we hope after some training, $G$ can generate samples that are indistinguishable from the real. Mathematically, GANs learn $G$ and $D$ by solving:

$$
\underset{G}{\min}\underset{D}{\max} V(D, G) = E_{\bold{x}\sim p_{\text{data}}(\bold{x})}[\log(D(\bold{x}))] + E_{\bold{z}\sim p_z(\bold{z})}[\log(1-D(G(\bold{z})))]
$$

where $\bold{x}\sim p_{\text{data}}(\bold{x})$ denotes sampling from real data and $\bold{z}\sim p_z(\bold{z})$ denotes sampling from random distribution. Due to the nature of minimax game, training of GANs are often unstable and subject to many issues such as *mode collapsing*. Mode collapsing refers to a phenomena where generator produce a small set of plausible outputs that discriminator cannot distinguish, and generator may learn to produce only that small set of outputs (local minimum), then the discriminator's best strategy is to memorise that small set of output, now since the discriminator only remember only that small set of outputs, when the next iteration comes, the generator can easily produce plausible results. As a result the generator will only rotate through a set of outputs, and this is called mode collapse.
Among various technique, Midi Net applied *feature matching* and *one-sided label smoothing* to the training process to avoid these effects.

Feature matching [@salimansImprovedTechniquesTraining2016] refers to adding a L2 regularisers as follows:

$$
\lambda_1||\mathbb{E} \bold{X} - \mathbb{E} G(\bold{z})||^2_2+\lambda_2||\mathbb{E}f(\bold{X}-\mathbb{E}f(G(\bold{z}))||^2_2
$$

where $f$ denotes the first convolutional layer in the discriminator and $\lambda_1$ and $\lambda_2$ are hyper parameters that weight the contribution. We can easily deduce that by adding the above loss, the model will try to match the distribution of real and generated data.

One-sided label smoothing [@salimansImprovedTechniquesTraining2016] is used to avoid overconfidence of the discriminator, this means that the discriminator only use a subset of the input feature to detect real samples, and the generator is then in turn only generate these features since others are not used by the discriminator, what one-sided label smoothing does is that it penalise the discriminator when the prediction goes over $0.9$, this can be done easily by setting the output true label to be $0.9$ instead of $1.0$.

The generator consists of 4 layers of transposed convolutional layer, each conditioned on the chord to generate corresponding melody, the chord condition is also feed into 4 layers of convolution, each of the output are concatenated to the input before feeding into the transposed convolution. Transpose convolution is originally used in CNN network for up-sampling, you can think of it as reverse operation of the convolution layer. The discriminator is simply two convolutional layers follow by a dense layer, its input also has the chord condition, there is also a 1D condition that encode any prior knowledge such as musical scale and profiles of the melody.

![System diagram of the proposed MidiNet model for symbolic-domain music generation[^midinet]](c680e933a4bbb726c422add67cfcae46.png)

[^midinet]: https://arxiv.org/pdf/1703.10847.pdf

To trade-off between creativity and discipline, one can use only part of the intermediate 2D chord condition or decrease the values of $\lambda_1$ and $\lambda_2$ to control the feature matching restriction to give more freedom to the model.

In the experiment, they compare the performance of the models by finding people to rate their samples, the result shows that MidiNet performs comparably with MelodyRNN models in being pleasant and realistic while being much more interesting. Moreover, their model is only trained on 500+ melody tabs while the MelodyRNN model was trained on thousands of samples, though the exact number were not disclosed.

![Result of a user study comparing MelodyRNN and MidiNet models[^midinet]](b1db51e0530aff21dece87ab03573483.png)

*MidiNet 1* denote the model with only melody conditioning, and *MidiNet 2* denotes the model with additional chord conditioning, and the *Lookback* and *Attention* model are the MelodyRNN models that I introduced before. You can find more information by reading their paper[^midinet].


#### Music VAE - 2018

MusicVAE [@robertsHierarchicalLatentVector2019] is a work that explore the possibilities of modelling long-term structure in music with *hierarchical* decoder. It is hierarchical because unlike traditional VAE, it uses of several levels of indenpendent LSTMs for decoding. Although this work does not generate the music, it provides a powerful way to decode the latent representation while avoiding the vanishing influences of the latent space which many models encountered.

*VAE* refers to variational auto-encoder. Auto-encoder is a type of unsupervised learning architecture that attempts to learn a efficient representation of the given data, typically used for dimensionality reduction, and *variational* refers to a type of auto-encoder that learns the representation through a continuous distribution space rather than a discrete space, allow for a more natural representation. Although VAE has proven to be an effective model for producing semantically meaningful latent representation, it was found to be limited to sequential data and models at that time and have difficulty modelling sequences with long-term structure.

To understand the problem, let me first explain some background. When modelling long sequences, often researchers have to first perform some kind of dimensionality reduction to the input, in short this is because meaningful representation for human often contains verbose information for machine, leading to computational inefficiency. VAE has been proven to encode semantically meaningful latent representation in many tasks therefore researchers attempted to apply it in music generation. But for a generative task like music generation, RNN has been proven to be effective. However, since RNN itself is strong enough, when it is combined with VAE and used within the decoder for reconstruction, it becomes short-sighted and starts to ignore the information provided in the latent vector, leading to bad result in long-term. This is commonly called *posterior collapsing*. Posterior collapsing can result in the model producing plausible data while not able to encode a useful latent space.

In a typical recurrent VAE, the encoder and decoder often consists of stacked RNN. MusicVAE has a similar setting for the encoder but introduces a new architecture of the decoder, which attempts to avoid posterior collapsing.

![Schematic of our hierarchical recurrent Variational Autoencoder model[^music_vae]](757eb03e49dc995838a50c01469f6773.png)


[^music_vae]: https://arxiv.org/pdf/1803.05428.pdf

The encoder is simply a two-layer bidirectional LSTM [@article] network, the input sequence $\bold x = \{x_1,\dots,x_T\}$ is past into the layers to obtain the final state vectors $h_{i\in T}$,  these vectors are then concatenated and feed into two fully connected layers to produce the latent distribution $\mu$ and $\sigma$.

$$
\begin{aligned}
\mu &= W_{h\mu}h_T+b_\mu\\
\sigma &=\log(\exp(W_{h\sigma}h_T+b_\sigma)+1)
\end{aligned}
$$

where $W$ denotes learnable parameters and $b$ denotes bias. 

The bidirectional LSTM is similar to LSTM, except that it uses two layer of LSTM cells and runs your input in two ways: one from past to future which is same as LSTM, another one from future to past, remembering information in the future, allow the model to better understand the context, though the exact reason for why it works well is not clear yet. Since bidirectional LSTM requires both past and future context, it is not suitable for some tasks such as natural language predication and question-answering. However, an architecture called BERT [@devlinBERTPretrainingDeep2019] avoided this problem, you can read more about it if you are interested.

Now let’s introduce the decoder. The output state is segmented $\bold{x}$ into $U$ parts:

$$
\begin{aligned}
y_u &= \{x_{i_u},x_{i_u+1},\dots,x_{i_{u+1}-1}\}\\
\rightarrow \bold{x} &=\{y_1,y_2,\dots,y_U\}
\end{aligned}
$$

each of them is passed into the conductor, the conductor is a two layer unidirectional RNN, its initial state is computed by passing the latent variable $z$ into a fully connected layer then follow by a $\tanh$ activation layer. The conductor layer then receives the segmented $\bold{x}$ as a sequence and produce $U$ embedding vector $\bold{c} = \{c_1,c_2,\dots,c_U\}$.

These $U$ embedding vectors are passed through a shared fully connected layer then follow by a $\tanh$ activation layer, which produce the initial state for the final bottom layer which is a two layer unidirectional LSTM. At each step, these initial states are concatenated with the previous output state to form the input.

In theory, this architecture still allows for posterior collapsing, but by limiting the scope of the decoder, it forces the model to learn from the latent code. This is done by only allow the bottom level decoder to propagate vector in the subsequence. This can be done easily in CNN architecture, by simply reducing the receptive field, but no direct method exists for RNN. Note that we cannot just have reduce the context size to the RNN as we like to preserve the long-term context while limiting its receptive field, in theory the RNN has unlimited receptive field to the input sequence.

There are also attempts to pass the state of the decoder back to the conductor at the end of each sub-sequence, hope that the model would relate the output with the latent code, but found to exhibits worse performance. The reconstruction was highly accurate for short sequences and was able to produce compelling interpolations and samples. This shows that the model was able to learn effectively from its latent code without suffering posterior collapsing, this is further evidenced by the small difference in teacher-forced and sampled accuracy.

![Example interpolation in the 2-bar melody MusicVAE latent space.[^music_vae][^music_vae_online_supplement]](79e5829383fd73b2adbf4960497ac373.png)

![Reconstruction accuracies calculated both with teacherforcing (i.e., next-step prediction) and full sampling. All values are reported on a held-out test set. A softmax temperature of 1.0 was used in all cases, meaning we sampled directly from the logits[^music_vae]](e5e65e072966762f915240d8c62f261d.png)

[^music_vae_online_supplement]: https://storage.googleapis.com/magentadata/papers/musicvae/index.html


#### Muse Net - 2018

Before we look into this paper, let’s first have some background about the *attention* mechanism and *sparse* *transformer*.

Attention is a term that first introduced in 2016, used in machine translation that extends the existing encoder-decoder RNN-based architecture by allowing the model to search for parts in the source sentence that are relevant to the translation to the target sentence. Surprisingly, this attention mechanism was shown to be far more useful than just machine translation, in particular, *self-attention* (or intra-attention), a type of attention that relate different part of a sequence and compute a representation, has been used successfully in many tasks… (to be continued)




- a GPT-2 like architecture


#### JukeBox - 2020

- encoder decoder with dilated convolution
- vqvae to encode latent space.
- STFT to learn different pitch.


### Limitation & Motivation

- monotonic melody achieved good result but it is not so interesting
- most chords are encoded, does not allow the model to generate new chords
- raw audio requires long time to train and generate
- experiment with multi track multi chord generation.

## Experiment

### Monotonic Melody

- starter experiment
- work reasonably well, but easily overfit and does not capture the audio well 

### MidiNet-like Architecture

- GAN architecture
- reveal the hardness of generating multi-track/chord audio
- quickly abandon

### Causal Dilated Convolutions

- able to reproduce piano-roll with high accuracy
- requires very long time to train
- tends to overfit

### RNNs

- require less time to train
- able to reproduce piano-roll and capture rhythm

### Modified RNN

- able to capture rhythm and produce limited amount of creativity.

## Conclusion

### Summary

### Future Directions

### Reflections


# References


