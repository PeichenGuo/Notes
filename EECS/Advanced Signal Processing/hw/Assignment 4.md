## 4.1 Summary
Firstly, we learned about the Haar wavelet transform. It basically is mapping a signal to an orthobasis in a Helbert space. After learning 3 weeks about linear algebra, the wavelet transform connect the abstract mathmatic to siganl processing. It should be very useful as I can imagine.

Then we learned about othonormal wavelet basis, which I do not really understand. 

After That we learned about the important inverse problems. This section introduces some example of using linear inverse problem to simplify a question. 
### 4.2 
![[Pasted image 20230418112220.png]]
(1) $\rightarrow$ (2)
$$
\begin{aligned}
||f(x)||_H^2 &= \langle f(x), f(x) \rangle_H \\
&= \langle \sum_{i=1}^Nx_if(e_i), \sum_{j=1}^Ny_jf(e_j) \rangle_H \\
&= \sum_{i=1}^N\sum_{j=1}^N \langle x_if(e_i), y_jf(e_j) \rangle_H \\
&= \sum_{i=1}^N\langle x_if(e_i), y_if(e_i)\rangle_H  \\
&= \sum_{i=1}^Nx_iy_i \\
&= \sum_{i=1}^N\langle x_ie_i, y_ie_i\rangle_H  \\
&= \sum_{i=1}^N\sum_{j=1}^N\langle x_ie_i, y_je_j\rangle_H  \\
&= \langle \sum_{i=1}^Nx_ie_i, \sum_{j=1}^Ny_je_j \rangle_H \\
&= \langle x, x \rangle_H \\
&= || x ||_H^2
\end{aligned}
$$

Since $||f(x)||_H \ge 0$ and$||x||_H \ge 0$ , so we have $||f(x)||_H = ||x||_H$

(2) $\rightarrow$ (1)
let $i \neq j$
$$
\begin{aligned}
|| f(e_i) + f(e_j) ||_H^2 + || f(e_i) - f(e_j) ||_H^2 &= 
\langle f(e_i), f(e_j)\rangle_H + \overline{\langle f(e_i), f(e_j)\rangle_H} + 2Re\{\langle f(e_i), f(e_j)\rangle_H\} \\ 
&+ \langle f(e_i), f(e_j)\rangle_H + \overline{\langle f(e_i), f(e_j)\rangle_H} - 2Re\{\langle f(e_i), f(e_j)\rangle_H\} \\
&= 2\langle f(e_i), f(e_j)\rangle_H + 2 \overline{\langle f(e_i), f(e_j)\rangle_H} \\
&= 4\langle f(e_i), f(e_j)\rangle_H\\
\end{aligned}
$$
So,
$$
\begin{aligned}
\langle f(e_i), f(e_j)\rangle_H &= {1\over 4}(|| f(e_i) + f(e_j) ||_H^2 + || f(e_i) - f(e_j) ||_H^2) \\
&= {1\over 4}(|| f(e_i) + f(e_j) ||_H^2 + || f(e_i) - f(e_j) ||_H^2)
\end{aligned} \tag{1}
$$
Now lets take a look at $f(e_i) + f(e_j)$ :
$$
\begin{aligned}
e_i = {(e_i + e_j) \over 2} + {(e_i - e_j) \over 2}\\
e_j = {(e_i + e_j) \over 2} - {(e_i - e_j) \over 2}
\end{aligned}
$$
Since $f$ is a linear mapping:
$$
\begin{aligned}
f(e_i) &= f({(e_i + e_j) \over 2} + {(e_i - e_j) \over 2})\\
&= f({(e_i + e_j) \over 2})+ f({(e_i - e_j) \over 2}) \\
\end{aligned}
$$
So,
$$
\begin{aligned}
f(e_j) &= f({(e_i + e_j) \over 2})- f({(e_i - e_j) \over 2}) \\
\end{aligned}
$$
Then we have:
$$
\begin{aligned}
f(e_i) + f(e_j) &= f({(e_i + e_j) \over 2})- f({(e_i - e_j) \over 2}) + f({(e_i + e_j) \over 2})+ f({(e_i - e_j) \over 2})\\
&= 2f({(e_i + e_j) \over 2}) \\
&= f{(e_i + e_j)} 
\end{aligned} \tag{2}
$$
Take (2) into (1):
$$
\begin{aligned}
\langle f(e_i), f(e_j)\rangle_H 
&= {1\over 4}(|| f(e_i) + f(e_j) ||_H^2 + || f(e_i) - f(e_j) ||_H^2) \\
&= {1\over 4}(|| f(e_i + e_j) ||_H^2 + || f(e_i - e_j) ||_H^2) \\
&= {1\over 4}(|| e_i + e_j ||_H^2 + || e_i - e_j ||_H^2) \\
&= \langle e_i, e_j \rangle_H \\
&= 0
\end{aligned} \tag{1}
$$
### 4.3
![[Pasted image 20230418152527.png]]
##### a)
We first calculate $\phi_{2,0}$, $\phi_{2,1}$, $\phi_{2,2}$, $\phi_{2,3}$:
$$
\begin{equation}
\phi_{2,0}= \left\{
\begin{aligned}
2&, t \in [0, {1\over4}) \\
0&, others \\
\end{aligned}
\right. 
\end{equation}
$$
$$
\begin{equation}
\phi_{2,1}= \left\{
\begin{aligned}
2&, t \in [{1\over4}, {1\over2}) \\
0&, others \\
\end{aligned}
\right. 
\end{equation}
$$
$$
\begin{equation}
\phi_{2,2}= \left\{
\begin{aligned}
2&, t \in [{1\over2}, {3\over4}) \\
0&, others \\
\end{aligned}
\right. 
\end{equation}
$$
$$
\begin{equation}
\phi_{2,3}= \left\{
\begin{aligned}
2&, t \in [{3\over4}, 1] \\
0&, others \\
\end{aligned}
\right. 
\end{equation}
$$
So, we have $s_{2,0}, s_{2,1}, s_{2,2}, s_{2,3}:
$$
\begin{aligned}
s_{2,0} &= \langle x, \phi_{2,0}\rangle = 1 \\
s_{2,1}&= \langle x, \phi_{2,1}\rangle = 0 \\
s_{2,2} &= \langle x, \phi_{2,2}\rangle = 1 \\
s_{2,3} &= \langle x, \phi_{2,3}\rangle = {3\over2} \\
\end{aligned}
$$
So, we got $s_{1,0}, s_{1,1}$ and $w_{1,0}, w_{1,1}$:
$$
\begin{aligned}
s_{1,0} &= {1 \over \sqrt{2}} (\phi_{2,0} + \phi_{2,1}) 
= {1 \over \sqrt{2}}
\\
s_{1,1} &= {1 \over \sqrt{2}} (\phi_{2,2} + \phi_{2,3}) 
= {5 \over 2\sqrt{2}}
\\
w_{1,0} &= {1 \over \sqrt{2}} (\phi_{2,0} - \phi_{2,1}) 
= {1 \over \sqrt{2}}
\\
w_{1,1} &= {1 \over \sqrt{2}} (\phi_{2,2} - \phi_{2,3}) 
= -{1 \over 2\sqrt{2}}
\\
\end{aligned}
$$
Then we will got $s_{0,0}$:
$$
s_{0,0}= {1 \over \sqrt{2}} (\phi_{1,0} + \phi_{1,1}) 
= {7 \over 4}
$$
b)
$$
\begin{aligned}
\int x^2(t) dt &= s_{0,0}^2 + \sum_{j,n}w_{j,n}^2 \\
&= s_{0,0}^2 + \sum_{j = 0}^J \sum_{n=0}^{2^J} w_{j,n}^2
\end{aligned}
$$

### 4.4
![[Pasted image 20230418171035.png]]
$$
y[m,n]= {1\over|P_{mn}|} \sum_{m',n'\in P_{mn}} x[m',n'], 1 \le m,n \le N
$$

We can use a convolution core to accomplish this thing. 
The core will be in shape of RxR:
$$
{1\over R^2}
\left[
\begin{matrix}
 1      & 1      & \cdots & 1      \\
 1      & 1      & \cdots & 1      \\
 \vdots & \vdots & \ddots & \vdots \\
 1      & 1      & \cdots & 1      \\
\end{matrix}
\right]
$$

