# VAE_Ising-Model
use variational auto-encoders to deal with Ising models


Week 1: 3rd-9th,July
----
### introduction
When using Markov Chain Monte Carlo method, the sampling method is of crucial for the simulation efficiency. Only when the flip steps are repeated for enough times can the system reaches equilibrium. To generate enough independent samples at each temperature, the flip step should be repeated for more than ~M * N times, where M is the number of independent samples and N=I*J is the square lattice's grid scale.

Here I want to find a more efficient way to generate such simulated configurations using VAEs and GANs.

### VAEs for Ising model
Here are some previous work:

1.[1410.3831v1]An exact mapping between the Variational Renormalization Group and Deep Learning. The author showed the relationship between renormalization group(RG) and Restricted Boltzmann Machines(RBM) in Ising model.

2.[1606.00318]Discovering phase transitions with unsupervised learning. The author used principal component analysis(PCA) to find order parameters of Ising model.

3.[1605.01735]Machine learning phases of matter. The author identified phases and phase transitions in condensed matter systems via supervised machine learning.

4.[1703.02435]Unsupervised learning of phase transitions: from principal component analysis to variational autoencoders. The author used PCA, AE and VAE to learn about the latent parameters which best describe states of the two-dimensional Ising model and the three-dimensional XY model.

# 



