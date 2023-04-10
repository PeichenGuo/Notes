## 3.1 Summary
For the last 2  courses, we learned about vector space. 
We first introducerd the concept of vector space. A vector space is a collection of things that obey certain abstact.  We also introduced the concept of vector addition and scalar multiplication which define the vector space. Then we learned concepts like lineraly dependent and basis.
After that, we leared about norms. A norm on a linear space S is a mapping form S to R. Then we introduced the concept of inner product, which is a mapping form S x S to C. After that we leared about the induced norm which is the norms defined by inner products.  Then we learned about the completeness of a linear space.
Then we leared linear approximation in Hilbert space and orthogonal projection. Then we introduced Gram-Schemidt algorithm.

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

## 3.3
![[Pasted image 20230410160735.png]]
Acroding to the defination of inner product, the induced norm is:
$$
\begin{aligned}
||x|| &= (<x, x>)^{1\over2} \\
&= \sqrt{\int_{-1}^{1}{f(x)}^2dx}
\end{aligned}
$$
$S_3$ is {$1, x, x^2, x^3$}.
First, we calculate $u_1$:
$$
\begin{aligned}
w_1 &= 1 \\
u_1 &= {1 \over {||1||}} \\
&= {1 \over {\sqrt{\int_{-1}^{1}1dx}}} \\
&= {\sqrt{2} \over 2}
\end{aligned}
$$
Then, we calculate $u_2$:
$$
\begin{aligned}
w_2 &= v2 - <v2, u1>u1 \\
&= x - {\sqrt{2} \over 2}{\int_{-1}^{1}{ {\sqrt{2} \over 2}x}dx}\\
&= x\\
u_2 &= {{x} \over {||{x}||}} \\
&= {x \over {\sqrt{\int_{-1}^{1}{x}^2dx}}} \\
&= {\sqrt{6} \over 2}x
\end{aligned}
$$
Then, we calculate $u_3$:
$$
\begin{aligned}
w_3 &= v3 - <v3, u2>u2  - <v3, u1>u1 \\
&= x^2 
- {\sqrt{6} \over 2} x{\int_{-1}^{1}{ {\sqrt{6} \over 2}x^3}dx}
- {\sqrt{2} \over 2}{\int_{-1}^{1}{ {\sqrt{2} \over 2}x^2}dx}
\\
&= x^2 
- {1 \over 2}{\int_{-1}^{1}{x^2}dx}
\\
&= x^2 - {1\over3}\\
u_2 &= {{x^2 - {1\over3}} \over {||{x^2 - {1\over3}}||}} \\
&= {x^2 - {1\over3} \over {\sqrt{\int_{-1}^{1}{(x^2 - {1\over3})}^2dx}}} \\
&= {3\sqrt{10} \over 4}x^2 - {\sqrt{10} \over 4} \\
&= {\sqrt{10} \over 4}(3x^2 - 1)
\end{aligned}
$$
Then, we calculate $u_4$:
$$
\begin{aligned}
w_4 &= v3 - <v4, u3>u3 - <v4, u2>u2  - <v4, u1>u1 \\
&= x^3
- ({3\sqrt{10} \over 4}x^2 - {\sqrt{10} \over 4}){\int_{-1}^{1}{ ({3\sqrt{10} \over 4}x^2 - {\sqrt{10} \over 4})x^3}dx}
- {\sqrt{6} \over 2} x{\int_{-1}^{1}{ {\sqrt{6} \over 2}x^4}dx}
- {\sqrt{2} \over 2}{\int_{-1}^{1}{ {\sqrt{2} \over 2}x^3}dx}
\\
&= x^3
- {\sqrt{6} \over 2} x{\int_{-1}^{1}{ {\sqrt{6} \over 2}x^4}dx}
\\
&= x^3 - {3\over5}x\\
u_2 &= {{x^3 - {3\over5}x} \over {||{x^3 - {3\over5}x}||}} \\
&= {x^3 - {3\over5}x \over {\sqrt{\int_{-1}^{1}{(x^3 - {3\over5}x)}^2dx}}} \\
&= {5\sqrt{14} \over 4}x^3 - {3\sqrt{14} \over 4}x \\
&= {\sqrt{14} \over 4}(5x^3 - 3x)
\end{aligned}
$$
So the orthobasis is:
$$
<{\sqrt{2} \over 2}, {\sqrt{6} \over 2}x, {\sqrt{10} \over 4}(3x^2 - 1), {\sqrt{14} \over 4}(5x^3 - 3x)>
$$
## 3.4
![[Pasted image 20230410172125.png]]
