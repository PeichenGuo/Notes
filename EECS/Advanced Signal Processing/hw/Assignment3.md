## 3.1 Summary
For the last 2  courses, we learned about vector space. 
We first introducerd the concept of vector space. A vector space is a collection of things that obey certain abstact.  We also introduced the concept of vector addition and scalar multiplication which define the vector space. Then we learned concepts like lineraly dependent and basis.
After that, we leared about norms. A norm on a linear space S is a mapping form S to R. Then we introduced the concept of inner product, which is a mapping form S x S to C. After that we leared about the induced norm which is the norms defined by inner products.  Then we learned about the completeness of a linear space.

## 3.2
![[Pasted image 20230410123144.png]]
#### (a)
To simplify the discussion, I will work on [-1,1] rather than [0, 1]. It is very obvious that $C([0,1]) \in C([-1, 1])$.
Let's assume that:
$$
\begin{equation}
f_n(x) = \left\{
\begin{aligned}
-1 &, x \in [-1, - {1 \over n}] \\
nx &, x \in [ - {1 \over n}, {1 \over n}] \\
1 &, x \in [{1 \over n}, 1]
\end{aligned}
\right. 
\end{equation}
$$
Let $n > m, t \in [0,1]$ we have:
$$
\begin{equation}
| f_n(t) - f_m(t) |= \left\{
\begin{aligned}
(nt - mt)&, t \in [0, {1 \over n}] \\
(1 - mt)&, x \in [ {1 \over n}, {1 \over m}] \\
0 &, x \in {1 \over m}, 1]
\end{aligned}
\right. 
\end{equation}
$$

So we have:
$$
\begin{aligned}
||f_n - f_m||_2 &= ({\int_{0}^{1}{| f_n(t) - f_m(t)|^2}dt})^{1/2} \\
&= ({\int_{0}^{1\over n}{(nt - mt)^2}dt} + {\int_{1 \over n}^{1\over m}{(1 - mt)^2}dt})^{1/2} \\
& = {m \over {3 n^2}} + {1 \over {3m}} - {2\over{3n}} \\
& = {2\over3}({2m \over {n^2}} + {2 \over {m}} - {1\over{n}}) \\
& < {2m \over {n^2}} + {2 \over {m}} - {1\over{n}} \\
& < {2 \over {n}} + {2 \over {m}} - {1\over{n}} \\
& = {1\over n} + {2\over m} \\
& < {3\over n}
\end{aligned}
$$
$\forall \epsilon, N > {3over \epsilon}, N \in \mathbb{N}$, we have:
$$
\begin{aligned}
{3\over n} < {3 \over {3\over \epsilon}} = \epsilon
\end{aligned}
$$
So the $f_n$ I defined before is a Cauchy sequence. However, consider $n \rightarrow \inf$, 
$$
\begin{equation}
f_n(x) = \left\{
\begin{aligned}
-1 &, x \in [-1, -0) \\
0 &, x = 0\\
1 &, x \in (0, 1]
\end{aligned}
\right. 
\end{equation}$$
we find that $f_n(x) \notin C[(0,1)]$. 
#### (b)
Let $\{f_n(x)\}^{\inf}_{n=1}$ is a Cauchy sequence in $C([0,1])$ . By defination, for every $\epsilon > 0$, there is an N such that $||f_n - f_m|| < \epsilon$ for all $n,m \ge N$. For any fixed $t \in [0,1]$ , this implies that 
$$
|fn(t) - fm(t)| < \epsilon, \forall m, n \ge N.
$$
So, $\{f_n(x)\}^{\inf}_{n=1}$ is a Cauchy sequence of real number. 