## 4.1 Summary
Firstly, we learned about the Haar wavelet transform. It basically is mapping a signal to an orthobasis in a Helbert space. After learning 3 weeks about linear algebra, the wavelet transform connect the abstract mathmatic to siganl processing. It should be very useful as I can imagine.

Then we learned about othonormal wavelet basis, which I do not really understand. 

After That we learned about the important inverse problems. This section introduces some example of using linear inverse problem to simplify a question. 
### 4.2 
![[Pasted image 20230418112220.png]]
##### 4.2.1
Let's say $i \neq j$  
$$
\begin{aligned}
\langle f(e_i), f(e_j) \rangle &= 
\langle f(e_i), f(e_j) - \sum_{k=1}^{N}\langle f(e_j),e_k\rangle_H e_k + \sum_{k=1}^{N}\langle f(e_j),e_k\rangle_H e_k \rangle
\end{aligned} \tag{1}
$$
consider $f(e_j) - \sum_{k=1}^{N}\langle f(e_j),e_k\rangle_H e_k + \sum_{k=1}^{N}\langle f(e_j),e_k\rangle_H e_k$, it is simply $f(e_j) = v + w$, where $v = f(e_j) - \sum_{k=1}^{N}\langle f(e_j),e_k\rangle_H e_k$ and $w = \sum_{k=1}^{N}\langle f(e_j),e_k\rangle_H e_k$

First let's take a look into $w$, $\sum_{k=1}^{N}\langle f(e_j),e_k\rangle_H e_k$ is in $\{e_i\}_{i=1}^N$. Since $f$ preserves orthogonality,  
$$\langle f(e_i), \sum_{k=1}^{N}\langle f(e_j),e_k\rangle_H e_k \rangle = 0 \tag{2}$$

Then let's take a look into v. 
$$
\begin{aligned}
\langle v, e_i\rangle &= 
\langle f(e_j) - \sum_{k=1}^{N}\langle f(e_j),e_k\rangle_H e_k, e_i\rangle \\
&= \langle f(e_j), e_i \rangle - \langle \sum_{k=1}^{N}\langle f(e_j),e_k\rangle_H e_k, e_i\rangle \\
\end{aligned}
$$
Since $\{e_i\}_{i=1}^N$ is orthobasis, $\langle \sum_{k=1}^{N}\langle f(e_j),e_k\rangle_H e_k, e_i\rangle = \langle f(e_j), e_i \rangle$.
So, 
$$
\begin{aligned}
\langle v, e_i\rangle &= \langle f(e_j) - \sum_{k=1}^{N}\langle f(e_j),e_k\rangle_H e_k, e_i\rangle  \\
&= \langle f(e_j), e_i \rangle - \langle f(e_j), e_i \rangle \\
&= 0
\end{aligned} 
$$
Since $f$ preserves orthogonality, $\langle v, f(e_i)\rangle  = 0$, i.e：
$$
\langle f(e_j) - \sum_{k=1}^{N}\langle f(e_j),e_k\rangle_H e_k, f(e_i)\rangle \tag{3}
$$
From (1) (2) (3), we can say:
$$
\begin{aligned}
\langle f(e_i), f(e_j) \rangle &= 
\langle f(e_i), f(e_j) - \sum_{k=1}^{N}\langle f(e_j),e_k\rangle_H e_k + \sum_{k=1}^{N}\langle f(e_j),e_k\rangle_H e_k \rangle \\
&= \langle f(e_i), f(e_j) - \sum_{k=1}^{N}\langle f(e_j),e_k\rangle_H e_k \rangle + 
\langle f(e_i), \sum_{k=1}^{N}\langle f(e_j),e_k\rangle_H e_k \rangle
= 0
\end{aligned} 
$$
It is obvious to prove the case when $i=j$ using the same method.


##### 4.2.2


### 4.3
![[Pasted image 20230418152527.png]]
##### a)


### 4.4
![[Pasted image 20230418171035.png]]
