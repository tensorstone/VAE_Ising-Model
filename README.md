# VAE_Ising-Model
use variational auto-encoders to deal with Ising models


Week 1: 3rd-9th,July
----
### introduction
When using Markov Chain Monte Carlo method, the sampling method is of crucial for the simulation efficiency. Only when the flip steps are repeated for enough times can the system reaches equilibrium. To generate enough independent samples at each temperature, the flip step should be repeated for more than ~M * N times, where M is the number of independent samples and N=I*J is the square lattice's grid scale.

Here I want to find a more efficient way to generate such simulated configurations using VAEs and GANs.

### Here are some previous work:
1.[1410.3831v1]An exact mapping between the Variational Renormalization Group and Deep Learning. The author showed the relationship between renormalization group(RG) and Restricted Boltzmann Machines(RBM) in Ising model.

2.[1606.00318]Discovering phase transitions with unsupervised learning. The author used principal component analysis(PCA) to find order parameters of Ising model.

3.[1605.01735]Machine learning phases of matter. The author identified phases and phase transitions in condensed matter systems via supervised machine learning.

4.[1703.02435]Unsupervised learning of phase transitions: from principal component analysis to variational autoencoders. The author used PCA, AE and VAE to learn about the latent parameters which best describe states of the two-dimensional Ising model and the three-dimensional XY model.

### VAEs for Ising model
The configuration generated by VAEs in 1703.02435 can not approximate the real images from a mathematical perspective. This week I concentrate on finding the reason of this phenomenon.

I duplicated the VAE in the paper. In the figure below, the X-axis is the hidden variable in the hidden layer of my VAE and the Y-axis is the magnetic moment.

![image](https://github.com/tensorstone/VAE_Ising-Model/blob/master/A%20line%E4%B8%80%E6%9D%A1%E7%9B%B4%E7%BA%BF.png)

![image](https://github.com/tensorstone/VAE_Ising-Model/blob/master/Generated_VAE%E7%94%9F%E6%88%90.png)

When I used the sign function to duplecate a configuration at a certain temperature, I got this

![image](https://github.com/tensorstone/VAE_Ising-Model/blob/master/Gen2.png)
![image](https://github.com/tensorstone/VAE_Ising-Model/blob/master/Gen1.png)

Obviously, VAE itself performs well in classification tasks, but performs poorly in replication tasks.

### Math perspective
![image](https://github.com/tensorstone/VAE_Ising-Model/blob/master/NetVisualize.png)

Here the KL-Divergence(KL-term for short) constrain the mapping process from the initial distribution to a standard Normal Distribution

