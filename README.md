# VAE_Ising-Model
use variational auto-encoders to deal with Ising models


Week 1: 3rd-9th,July
----
### 1. introduction
When using Markov Chain Monte Carlo method, the sampling method is of crucial for the simulation efficiency. Only when the flip steps are repeated for enough times can the system reaches equilibrium. To generate enough independent samples at each temperature, the flip step should be repeated for more than ~M * N times, where M is the number of independent samples and N=I*J is the square lattice's grid scale.

Here I want to find a more efficient way to generate such simulated configurations using VAEs and GANs.

### 2. Here are some previous work:
1.[1410.3831v1]An exact mapping between the Variational Renormalization Group and Deep Learning. The author showed the relationship between renormalization group(RG) and Restricted Boltzmann Machines(RBM) in Ising model.

2.[1606.00318]Discovering phase transitions with unsupervised learning. The author used principal component analysis(PCA) to find order parameters of Ising model.

3.[1605.01735]Machine learning phases of matter. The author identified phases and phase transitions in condensed matter systems via supervised machine learning.

4.[1703.02435]Unsupervised learning of phase transitions: from principal component analysis to variational autoencoders. The author used PCA, AE and VAE to learn about the latent parameters which best describe states of the two-dimensional Ising model and the three-dimensional XY model.

### 3. AE/VAEs for Ising model
The configuration generated by VAEs in 1703.02435 can not approximate the real images from a mathematical perspective. This week I concentrate on finding the reason of this phenomenon.

I duplicated an AE mentioned in the paper. In the figure below, the X-axis is the hidden variable in the hidden layer of my VAE and the Y-axis is the magnetic moment.

<img src="https://github.com/tensorstone/VAE_Ising-Model/blob/master/A%20line%E4%B8%80%E6%9D%A1%E7%9B%B4%E7%BA%BF.png" width=350 height=350 />

![image](https://github.com/tensorstone/VAE_Ising-Model/blob/master/Generated_VAE%E7%94%9F%E6%88%90.png)

When I used the sign function to duplecate a configuration at a certain temperature, I got this

![image](https://github.com/tensorstone/VAE_Ising-Model/blob/master/Gen2.png)
![image](https://github.com/tensorstone/VAE_Ising-Model/blob/master/Gen1.png)

Obviously, VAE itself performs well in classification tasks, but performs poorly in replication tasks.

### 4. Math perspective
![image](https://github.com/tensorstone/VAE_Ising-Model/blob/master/NetVisualize.png)

Here the KL-Divergence(KL-term for short) constrain the mapping process of each T from the initial distribution to a standard Normal Distribution, and the Binary_ term means binary_crossentropy. 

Different configurations in each T map to different mu and sigma, then sample a new point from this normal distribution. The first figure in this part shows that the hidden variable is approximately an uniform distribution. The reason is our Ts are continous, so that the configurations of near Ts are hard to be separated.(Many Gauss distributions consist of a uniform distribution)

We sample hidden variables near z_mean. To reproduce the initial configuration at high accuracy, we must separate different T's z_mean_Ts. Here the Binary_ loss plays its role. 

In this framework, the loss function lead to a result of the separation of the experimental samples at various temperatures. But if the aim is reproduce a fake but true figure, we shall use other loss functions.

Week 2: 10th-16th,July
----
### 5. More from mathematical perspective

In AEs, the optimization objective is to minimize the cross entropy between the inputs and the outputs. A certain configuration is mapped to a certain point in the hidden layer, which turns out to be the magnetic moment.

In VAEs, the optimization objective is to minimize the corss entropy between the inputs and the configuration generated from a Gaussian re-sample process in the hidden layer. So, a certain configuration is mapped to an area with Gaussian probability density. Such configuration may be re-sampled to each of those points, and vise versa. It's because such configuration may come from any magnetic moment those points represent. This leads to an expansion in the hidden layer-magnetic moment graph.

<img src="https://github.com/tensorstone/VAE_Ising-Model/blob/master/ae_ising.png" width=350 height=350 /><img src="https://github.com/tensorstone/VAE_Ising-Model/blob/master/vae_ising_1.png" width=350 height=350 />

If we can sample from a Gaussian distribution with great variance and reproduce the initial configuration, we may draw a conclusion that the configuration within this Gaussian should be alike. Sothat the variance here represents the extent of the magnetic moment near this temperature. We may find the critical temperature clearly from this figure:


<img src="https://github.com/tensorstone/VAE_Ising-Model/blob/master/vae_ising_var.png" width=350 height=350 />


So, if we set the target to precisely produce a picture near a certain temperature, we should use AEs instead of VAEs.

### 6. Improvement: loss function

[https://github.com/tensorstone/VAE_Ising-Model/blob/master/AE%20Ising%20and%20collapse.ipynb]

Now our aim changes from classification to reproduction, we may use our knowledge in physics to improve the precision.So first, I chose to use a new loss function of

Loss_M = M_input - M_output

 (To make this process seem more intelligent, we can use hidden variabels instead of M. But actually it's just a linear transformation, as is shown above.)

I also add another loss to the loss function:

Loss_constrain = -Sum((Output - 0.5)^2)

which works as a penalty term, and restrict the value of each spin to 0 or 1.

Loss = Loss_M + Loss_constrain

[What's wrong?]

One more experiment is about using the M itself to generate configurations. But failed even with transfer learning technics. It appears to converge to stable local minima easily.

<img src="[https://github.com/tensorstone/VAE_Ising-Model/blob/master/Generate%20with%20T%20and%20M.ipynb]
data:image/png;base64,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%0A" width=350 height=350 />

### 7. Scale expansion
Add some output units for scale expansion. But the problem now is mode collapse. Maybe I can try GAN to solve such problem

