<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>

# Sampling Octahedron Environment Map Lighting

I will explain how to get a beautiful envrionment lighting map in this artical.
The steps can be divided into: 

1. Define a rendering equation.
2. A brief review of Monte Carlo.
3. Prepare for solving the rendering equation.
4. Calculate a inverse CDF map.
5. Sampling.

The generated picture looks like this:
![map](./envlight.png)

## Rendering Equation

The Rendering equation used here is:

$$
\begin{align}
    L_o(v) &= L_e(v) + \int_{\Omega} f_r(v,l)L_i(l)(n \cdot l)d\omega  \\
\end{align}
$$

where $v,l$ is outgoing direction and negative incoming direction respectively. 
$L_o$ is outgoing radiance, $L_e$ is emitted radiance, $L_i$ is incoming radiance,
$\Omega$  is unit hemisphere, $f_r(v,l$ ) is BRDF, $n$ is surface normal, 
$\omega$ is solid angle on the hemisphere.

![rendering hemisphere]()

For environment map, emitted radiance is always be zero, so it can be omitted,
Then the final rendering equation used is the form without emitted radiance term:

$$
\begin{align}
    L_o(v) &= \int_{\Omega} f_r(v,l)L_i(l)(n \cdot l)d\omega  \\
\end{align}
$$

Notice what we want is the radiance of outgoing direction, and $l$ is negative direction of incoming direction.
Basically, the rendering equation defines what kind of operations we shold do in order to get the outgoing radiance.
But, what operations exactly? 

## Use Monte Carlo method!

I have briefly introduced [Monte Carlo method](https://waizui.github.io/posts/monte_carlo/monte_carlo.html) before,
So I will not introduce it again here. But here are some supplementary explanation.

For a Monte Carlo Integral:

$$ F_n = \frac{1}{n} \sum_{i=1}^{n} \frac{f(x_i)}{p(x_i)} $$

The best pdf $p$ is 

$$p(x) = \frac{f(x)}{\int_a^bf(x)dx}$$

this makes variance minium.

Next, for rendering equation, we can derive the form of integrating using spherical coordinates by substituting soild angle with 
$d\omega = sin(\theta) d\theta d\phi$, and the integral will become:

$$
\begin{align}
    L_o(v) &= \int_{\Omega} f_r(v,l)L_i(l)(n \cdot l)d\omega  \\
           &= \int_0^{2\pi} \int_0^{\frac{\pi}{2}}f_r(v,l)L_i(l)(n \cdot l)sin(\theta)d\theta d\phi
\end{align}
$$

Then use Monte Carlo method:

$$
\begin{align}
  L_o(v) &= \int_0^{2\pi} \int_0^{\frac{\pi}{2}}f_r(v,l)L_i(l)(n \cdot l)sin(\theta)d\theta d\phi \\
         & \approx \frac{1}{N} \sum_{n=0}^{N} \frac{f(\phi_n,\theta_n)}{pdf(\phi_n,\theta_n)} \\
         & \approx \frac{1}{N} \sum_{n=0}^{N} \frac{f_r(v,l)L_i(l)(n \cdot l)sin(\theta_n)}{pdf(\phi_n,\theta_n)}
\end{align}
$$


**to be continued...**
