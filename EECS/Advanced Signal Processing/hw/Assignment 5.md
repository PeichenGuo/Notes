## 5.1 Summary
We first introduce the concept of eigenvalue. Then we talked about eigenvalue decomposition. Knowing such technique can help us solve. In the case of y = Ax, when A is sym + def, we can use the eigenvalue decomposition to soleve the problem elegently.

In practice, the sym+def matrices are relatively rare to find. The singular value decomposition (SVD) takes apart an arbitrary MxN. We can use the SVD to tackle the general system of linear equations. 

Then we learned about pseudo inverse. 

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
