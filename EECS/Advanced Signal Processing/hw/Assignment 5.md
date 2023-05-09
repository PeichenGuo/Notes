## 5.1 Summary
We first introduce the concept of eigenvalue. Then we talked about eigenvalue decomposition. Knowing such technique can help us solve. In the case of y = Ax, when A is sym + def, we can use the eigenvalue decomposition to soleve the problem elegently.

In practice, the sym+def matrices are relatively rare to find. The singular value decomposition (SVD) takes apart an arbitrary MxN. We can use the SVD to tackle the general system of linear equations. 

Then we learned about pseudo inverse. 

After that we learned about stable reconstruction with the Tikhonov regularization.
## 5.2 SVD
![[Pasted image 20230508135642.png]]
Solve:
$$
\begin{aligned}
det(\lambda I - A) &= 
det(
\left [
\begin{matrix}
\lambda - 1 & -1 \\
-1 & \lambda - 1 \\
\end{matrix}
\right ] 
) \\
&= (\lambda_1 - 1)^2 - 1

\end{aligned}

$$
We get, 
$$
\begin{aligned}
\lambda_1 &= 2 \\
\lambda_2 &= 0 \\
\end{aligned}
$$
So, 
$$
\Sigma = 
\left [
\begin{matrix}
0 & 0 \\
0 & \sqrt{2} \\
\end{matrix}
\right ] 
$$
$$
A^{+} =
\left [
\begin{matrix}
1\over4 & 1\over4 \\
1\over4 & 1\over4 \\
\end{matrix}
\right ] 
$$
Now we solve:
$$
\begin{aligned}
(X^TX)V &= V\Lambda \\
\left [
\begin{matrix}
1 & 1 \\
1 & 1 \\
\end{matrix}
\right ] ^T
\left [
\begin{matrix}
1 & 1 \\
1 & 1 \\
\end{matrix}
\right ] V 
&= 
V
\left [
\begin{matrix}
2 & 0 \\
0 & 0 \\
\end{matrix}
\right ] \\
\left [
\begin{matrix}
1 & 1 \\
1 & 1 \\
\end{matrix}
\right ] 
\left [
\begin{matrix}
v_1 & v_2 \\
\end{matrix}
\right ]
&= 
\left [
\begin{matrix}
v_1 & v_2 \\
\end{matrix}
\right ]
\left [
\begin{matrix}
1 & 0 \\
0 & 0 \\
\end{matrix}
\right ]

\end{aligned}
$$
we have:
$$
V = \left [
\begin{matrix}
{\sqrt{2}\over2} & -{\sqrt{2}\over2} \\
{\sqrt{2}\over2} & -{\sqrt{2}\over2} \\
\end{matrix}
\right ]
$$
since V is symmetric, 
$$
U = V = \left [
\begin{matrix}
{\sqrt{2}\over2} & -{\sqrt{2}\over2} \\
{\sqrt{2}\over2} & -{\sqrt{2}\over2} \\
\end{matrix}
\right ]
$$
So, the SVD of A is:
$$
A = \left [
\begin{matrix}
{\sqrt{2}\over2} & -{\sqrt{2}\over2} \\
-{\sqrt{2}\over2} & -{\sqrt{2}\over2} \\
\end{matrix}
\right ]
\left [
\begin{matrix}
0 & 0 \\
0 & 2 \\
\end{matrix}
\right ]
\left [
\begin{matrix}
{\sqrt{2}\over2} & -{\sqrt{2}\over2} \\
-{\sqrt{2}\over2} & -{\sqrt{2}\over2} \\
\end{matrix}
\right ]
$$
So the pseudo inverse of A is:
$$
A^{+} =
\left [
\begin{matrix}
1\over4 & 1\over4 \\
1\over4 & 1\over4 \\
\end{matrix}
\right ] 
$$
We have:
$$
\begin{aligned}
r &= V\beta - V\Sigma V^T V \alpha \\
&= V(\beta - \Sigma \alpha) \\
&= VV^T(y - \Sigma x) \\
&= y - \Sigma x
\end{aligned}
$$
so:
$$
\begin{aligned}
||r||_2^2 &= ||y - \Sigma x||_2^2
\end{aligned}
$$
so, x is
$$
\begin {aligned}
x &= V\Sigma^{-1}V^Ty \\
&= A^{+}y \\
&= \left [
\begin{matrix}
1\over4 \\
1\over4 \\
\end{matrix}
\right ] 
\end{aligned}
$$
## 5.3 Convolution Theorem
![[Pasted image 20230509205849.png]]
### a
We have:
$$
\begin{aligned}
Hf_k &= \left [
\begin{matrix}
h[0] + h[N-1]e^{j2\pi k / N} + h[N-2]e^{j2\pi2k / N} + \cdots + h[1]e^{j2\pi2(N-1)k / N} \\
h[1] + h[0]e^{j2\pi k / N} + h[N-1]e^{j2\pi2k / N} + \cdots + h[2]e^{j2\pi2(N-1)k / N} \\
\vdots \\
h[N-1] + h[N-2]e^{j2\pi k / N} + h[N-1]e^{j2\pi2k / N} + \cdots + h[0]e^{j2\pi2(-1)k / N} \\
\end{matrix}
\right ] \\
&= (h[0] + h[N-1]e^{j2\pi2k / N} + \cdots + h[1]e^{j2\pi2(N-1)k / N}) f_k \\
&= \lambda_k f_k

\end{aligned}
$$
So $f_k$ is eigenvector. 
### b
$$
y = Hx = FDF^Hx
$$
We find that F is made up by fk and D is a diagonal matrix with eigenvalues like $(h[0] + h[N-1]e^{j2\pi2k / N} + \cdots + h[1]e^{j2\pi2(N-1)k / N})$. We knows that $f_k$ is discrete Fourier vector, so actually $F^Hx$ is actually doing DFT and $Fx$ is doing IDFT. While $Dx$ is like doing the multiplication. 
So, $FDF^Hx$ is like do a DFT first, and then do a multiplication and do an IDFT again. 
## 5.4 SVD Regularization
![[Pasted image 20230509210003.png]]
