## 1. Summary
We have just enjoyed a interesting class last week which divided us into 4 groups and compete with each other. I leared a lot on my topic, however, I failed to understand other groups' topic at once. 

Our topic is Recursive Least Square method which is pretty intuitive. The origin least square problem need a full matrix A with all of the observations in it which may take a lot of time to get the ovservations. So, this motivated recursive method to be carried out. The crucial idea of recursive method is that the observation and the calculation can be done parallely. Once some new obsevation is carried out, the matrix A can be updated and the $\hat{x}$ can be calculated.

## 2.
![[Pasted image 20230521165829.png]]
The least square problem is solving Ax = b and find the  $\hat{x}$ to minimize the Ax.
$$
\begin{aligned}
Ax &= b \\
QRx &= b \\
Q^TQRx &= Q^Tb \\
\end{aligned}
$$
since Q is orthogonal:
$$
\begin{aligned}
Q^TQRx &= Q^Tb \\
Rx &= Q^Tb\\
\end{aligned}
$$
Since A is a $m \times n$ matrix of rank n:
$$
\begin{aligned}
Rx &= Q^Tb\\
\left [
\begin{matrix}
U \\
R'
\end{matrix}
\right ] 
x &= 
\left [
\begin{matrix}
Q_1 \\
Q_2
\end{matrix}
\right ]^Tb \\

\end{aligned}
$$
U is a $n \times n$ matrix of rank n, R' is a $(m-n) \times n$ matrix. $Q_1$ is a $n \times n$ matrix, $Q_2$ is a $n \times (m-n)$
$$
\begin{aligned}
Ux &= Q_1^Tb \\
x &= U^{-1}Q_1^Tb \\
\\
R'x &= Q_2^Tb \\

\end{aligned}
$$
So, we have:
$$
||Ax-b||^2 = ||x-U^{-1}Q_1^Tb||^2 + ||Q_2^Tb-R'x||
$$
when $x = \hat{x}$, $||x-U^{-1}Q_1^Tb||^2  = 0$, $||Ax-b||^2  = ||Q_2^Tb-R'x||$, which is the minimal value.
So, $\hat{x}$ is the least square answer.

## 3. 
![[Pasted image 20230521193308.png]]
#### SD
step 2:
$$
\begin{aligned}
r_0 &= b - Hx_0 \\
&= 
\left[
\begin{matrix}
1 \\
0 \\
\end{matrix}
\right] 
-
\left[
\begin{matrix}
3 & -1 \\
-1 & 2 \\
\end{matrix}
\right]
\left[
\begin{matrix}
1 \\
1 \\
\end{matrix}
\right]\\
&= 
\left[
\begin{matrix}
-1 \\
-1 \\
\end{matrix}
\right] \\
\alpha_0 &= {r_0^Tr_0 \over r_0^THr_0} \\
&= 
{\left[
\begin{matrix}
-1 \\
-1 \\
\end{matrix}
\right] ^ T 
\left[
\begin{matrix}
-1 \\
-1 \\
\end{matrix}
\right]
\over
\left[
\begin{matrix}
-1 \\
-1 \\
\end{matrix}
\right] ^ T 
\left[
\begin{matrix}
3 & -1 \\
-1 & 2 \\
\end{matrix}
\right]
\left[
\begin{matrix}
-1 \\
-1 \\
\end{matrix}
\right]
} \\
&= 
{2\over3} \\
x_1 &= x_0 + \alpha_0 x_0 \\
&= 
\left[
\begin{matrix}
{1\over3} \\
{1\over3} \\
\end{matrix}
\right]
\end{aligned}
$$

step 2:
$$
\begin{aligned}
r_1 &= b - Hx_1 \\
&= 
\left[
\begin{matrix}
1 \\
0 \\
\end{matrix}
\right] 
-
\left[
\begin{matrix}
3 & -1 \\
-1 & 2 \\
\end{matrix}
\right]
\left[
\begin{matrix}
{1\over3} \\
{1\over3} \\
\end{matrix}
\right]\\
&= 
\left[
\begin{matrix}
{1\over3} \\
-{1\over3} \\
\end{matrix}
\right] \\
\alpha_0 &= {r_0^Tr_0 \over r_0^THr_0} \\
&= 
{
\left[
\begin{matrix}
{1\over3} \\
-{1\over3} \\
\end{matrix}
\right] ^ T 
\left[
\begin{matrix}
{1\over3} \\
-{1\over3} \\
\end{matrix}
\right]
\over
\left[
\begin{matrix}
{1\over3} \\
-{1\over3} \\
\end{matrix}
\right] ^ T 
\left[
\begin{matrix}
3 & -1 \\
-1 & 2 \\
\end{matrix}
\right]
\left[
\begin{matrix}
{1\over3} \\
-{1\over3} \\
\end{matrix}
\right]
} \\
&= 
0 \\
x_1 &= x_0 + \alpha_0 x_0 \\
&= 
\left[
\begin{matrix}
{1\over3} \\
-{1\over3} \\
\end{matrix}
\right]
\end{aligned}
$$
