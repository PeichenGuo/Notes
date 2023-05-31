## 1. Summary
We have just enjoyed a interesting class last week which divided us into 4 groups and compete with each other. I leared a lot on my topic, however, I failed to understand other groups' topic at once. 

Our topic is Recursive Least Square method which is pretty intuitive. The origin least square problem need a full matrix A with all of the observations in it which may take a lot of time to get the ovservations. So, this motivated recursive method to be carried out. The crucial idea of recursive method is that the observation and the calculation can be done parallely. Once some new obsevation is carried out, the matrix A can be updated and the $\hat{x}$ can be calculated.

Two groups discussed the Steepest Descent method and the Conjugate Gradient method. Those two methods basically are recursively approaching the least square problem. 

The last group, who is also the best presenter, carried out the kalman filter. Kalman filter is kind of like a better version of Recursive Least Square method. It requires less complexity.

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
step 1:
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
\alpha_1 &= {r_10^Tr_1 \over r_1^THr_1} \\
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
x_2 &= x_1 + \alpha_1 x_1 \\
&= 
\left[
\begin{matrix}
{1\over3} \\
-{1\over3} \\
\end{matrix}
\right]
\end{aligned}
$$
#### CG
init:
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
d_0 &= r_0 = \left[
\begin{matrix}
-1 \\
-1 \\
\end{matrix}
\right] \\
\end{aligned}
$$
step0:
$$
\begin{aligned}
\alpha_0 &= {r_0^Tr_0 \over d_0^THd_0} \\
&= {2\over3} \\
\\
x_1 &= x_0 + \alpha_0 d_0 \\
&= \left[
\begin{matrix}
{1\over3} \\
{1\over3} \\
\end{matrix}
\right] \\
\\
r_1 &= r_0 - \alpha_0 H d_0 \\
&= 
\left[
\begin{matrix}
{4\over3} \\
{2\over3} \\
\end{matrix}
\right]
\end{aligned}
$$
$$
\begin{aligned}
\beta_1 &= {r_1^Tr_1\over r_0^Tr_0} \\
&= {10\over9}
\\
d_1 &= r_1 + \beta_1d_0 \\
&= 
\left[
\begin{matrix}
{2\over9} \\
-{4\over9} \\
\end{matrix}
\right]
\end{aligned}
$$
step1:
$$
\begin{aligned}
\alpha_1 &= {r_1^Tr_1 \over d_1^THd_1} \\
&= 3 \\
\\
x_2 &= x_1 + \alpha_1 d_1 \\
&= \left[
\begin{matrix}
{1} \\
{-1} \\
\end{matrix}
\right] \\
\end{aligned}
$$

## 6.4 Kalman Filter
![[Pasted image 20230522125427.png]]
#### a.
We have the following at first:
$$
\begin{aligned}
y_0 = A_0x_0 + e_0 \\
\end{aligned}
$$
Then we have:
$$
\begin{aligned}
0 &= F_0x_0-x_1+\epsilon_0 \\
y_1 &= A_1x_1+e_1
\end{aligned}
$$
In other words:
$$
\begin{aligned}
\left[
\begin{matrix}
y_0 \\
0 \\
y_1 \\
\end{matrix}
\right]
&= 
\left[
\begin{matrix}
A_0 & 0 \\
F_0 & -I \\
0 & A_1 \\
\end{matrix}
\right]
\left[
\begin{matrix}
x_0 \\
x_1 \\
\end{matrix}
\right]
+
\left[
\begin{matrix}
e_0 \\
\epsilon_0 \\
e_1 \\
\end{matrix}
\right]
\end{aligned}
$$
We call the matrix $\bar{A_1}$:
$$
\left[
\begin{matrix}
A_0 & 0 \\
F_0 & -I \\
0 & A_1 \\
\end{matrix}
\right]
$$
We can calculate the least square problem by calculating:
$$
\left[
\begin{matrix}
\hat{x_{0|1}} \\
\hat{x_{1|1}} \\
\end{matrix}
\right]
=
(\bar{A_1}^T\bar{A_1})^{-1}\bar{A_1}^T\bar{y_k}
$$
#### b.
When k is very big, the following equations:
$$
x_{k+1} = F_kx_k+\epsilon_k
$$
$$
y_{k+1} = A_{k+1} x_{k+1} + e_{k+1}
$$
can be simplized into:
$$
x_{k+1} = x_k+\epsilon_k
$$
$$
y_{k+1} = x_{k+1} + e_{k+1}
$$
Now, $x_{k+1|k+1}$ is:
$$
x_{k+1|k+1} = K_{k+1} (y_{k+1} - x_{k|k}) + x_{k|k}
$$
where $K_{k+1}$ is Kalman gain which can be approximated to $\sqrt{5} - 1 \over 2$.

