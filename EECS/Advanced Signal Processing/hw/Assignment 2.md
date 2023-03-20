## 2.1 
#### (a) Summary
In the last week's course, we learned about Laplace Transform, DTFT/CTFT and sampling theorem. The sample theorem is crutial for me to understand ADC and DAC in some special chips like memory resistor nerual networks. 

In this week's course, we learned about vector space which is a pretty straightforward but important concept in linear algebra.

#### (b) ChatGPT


## 2.2 
![[Pasted image 20230320150159.png]]

#### (a)
$$
\begin{aligned}
\mathcal{F} [x_c(t)] &= {1\over2\pi} \mathcal{F}[Sa(4 \pi t)] * \mathcal{F}[cos(12 \pi t)] 
\\
&= {1\over2\pi} {\pi\over2}[sgn(\omega + 4\pi) - sgn(\omega - 4\pi)] * \pi [\delta (\omega - 12\pi) + \delta (\omega + 12\pi)] 
\\
&= {\pi\over 4}[sgn(\omega + 4\pi) * \delta (\omega - 12\pi) + sgn(\omega + 4\pi) * \delta (\omega + 12\pi) - sgn(\omega - 4\pi) * \delta (\omega - 12\pi) - sgn(\omega - 4\pi) * \delta (\omega + 12\pi)]
\\
&= {\pi\over 4} [sgn(\omega - 8\pi) + sgn(\omega + 16\pi) - sgn(\omega - 16\pi) - sgn(\omega + 8\pi)]
\end{aligned}
$$
So, we get:
$$
\begin{equation}
X_c(j\Omega) = \left\{
\begin{aligned}
{\pi\over2} &, \Omega \in (-16\pi, -8\pi], (8\pi, 16\pi] \\
0 &, \Omega \in others
\end{aligned}
\right. 
\end{equation}
$$
Thus:
$$
\begin{equation}
|X_c(j\Omega)| = \left\{
\begin{aligned}
{\pi\over2} &, \Omega \in (-16\pi, -8\pi], (8\pi, 16\pi] \\
0 &, \Omega \in others
\end{aligned}
\right. 
\end{equation}
$$
#### (b)
For T = $1\over16$, which is the Nyquist Interval, there is no alias . (The blue signal is the sampled signal).
![[Pasted image 20230320163110.png]]
For T = $1\over8$, there is no alias too.
![[Pasted image 20230320163145.png]]

#### (c)
let's assume $T_m$ is the minimal time interval and $\omega_m = {2\pi \over T_m}$ .
The Sampling is basicalling shifting the original signal right and left for $\omega_m$ in FD. 
So,
$$
\begin{aligned}
-\omega_1  \le  - \omega_2 + \omega_m \ &and \ -\omega_1 + n\omega_m \le \omega_1, n \in \mathbb{N} \\
\omega_2 - \omega_1 \le &\omega_m \le {2\over n}\omega_1, n \in \mathbb{N} 
\end{aligned}
$$
if we have $\omega_1 \le \omega_2 \le (1 + {2\over n})\omega_1$ , now $\omega_m = \omega_2 - \omega_1$.So, 
$$
T_m = {\pi \over {\omega_2 - \omega_1}}, \ \ \ {1\over{1 + {2\over n}}} \le {\omega_1\over\omega_2}, , n \in \mathbb{N} 
$$
We have:
$$
\lfloor {\omega_2 \over {\omega_2 - \omega_1}}\rfloor = \lfloor {1 \over {1 - {\omega_1 \over \omega_2}}} \rfloor \ge \lfloor {1 \over {1 - {1 \over (1 + {2\over n})}}} \rfloor \ge \lfloor 1.5 \rfloor = 2 ,  n \in \mathbb{N} 
$$
i.e. 
$$
\lfloor {\omega_2 \over {\omega_2 - \omega_1}}\rfloor \ge 2
$$
Let $m' ={ \omega_2 \over {\omega_2 - \omega_1}}$,
$$
T_m = {\pi \omega_2 \over ({\omega_2 - \omega_1})\omega_2} = {{m' \pi}\over \omega_2}
$$
because $n \in \mathbb{N}$,  m' can not get every value. So, let's say $m = \lfloor m' \rfloor$:
$$
T_m = {{m \pi}\over \omega_2}
$$
if $(1 + {2\over n})\omega_1 \le  3\omega_1 < \omega_2$, now $\omega_m = \omega_2 + \omega_1$. So, 
$$
T_m = {\pi \over {\omega_2 + \omega_1}}, \ \ {1\over3} < {\omega_1\over\omega_2}
$$
we have:
$$
\begin{aligned}
\lfloor {\omega_2 \over {\omega_2 - \omega_1}}\rfloor = \lfloor {1 \over {1 - {\omega_1 \over \omega_2}}} \rfloor < \lfloor {1 \over {1 - {1 \over 3}}} \rfloor = \lfloor 1.5 \rfloor = 2 \\
and \\
\lfloor {\omega_2 \over {\omega_2 - \omega_1}}\rfloor = \lfloor {1 \over {1 - {\omega_1 \over \omega_2}}} \rfloor > \lfloor {1\over{1-0}}  \rfloor = 1
\end{aligned}
$$
i.e.
$$
1 < {\omega_2 \over {\omega_2 - \omega_1}} < 1.5 \Rightarrow  \lfloor {\omega_2 \over {\omega_2 - \omega_1}}\rfloor = 1
$$
So,
$$
T = {{\pi m} \over \omega_2} = {{\pi} \over \omega_2} = {pi}
$$
which T becomes Nyquist interval. 

So, for both situation, 
$$
T_m = {{m \pi}\over \omega_2}, m = \lfloor {\omega_2 \over {\omega_2 - \omega_1}}\rfloor
$$
QED.

###  2.3
![[Pasted image 20230320180206.png]]
![[Pasted image 20230320180222.png]]
#### (a)
For $x_1(t)$, the sampling interval is less than Nyquist interval. So aliasing happened, which is shown in the graph below. 
![[Pasted image 20230320183312.png]]
The $|X_c(j\omega) - X_1(j\omega)|$ is shown in the graph too, which is :
$$
\begin{equation}
|X_c(j\omega) - X_1(j\omega)|  = \left\{
\begin{aligned}
-{1\over \pi}\omega - 3 &, \omega \in (-5\pi, -3\pi)\\
{1\over \pi}\omega - 3 &, \omega \in (3\pi, 5\pi)\\
0 &, \omega \in others
\end{aligned}
\right. 
\end{equation}
$$
$$
\begin{equation}
|X_c(j\omega) - X_1(j\omega)|^2  = \left\{
\begin{aligned}
{1\over \pi}\omega^2 + {6\over\pi}\omega + 9 &, \omega \in (-5\pi, -3\pi)\\
{1\over \pi}\omega^2 - {6\over\pi}\omega + 9&, \omega \in (3\pi, 5\pi)\\
0 &, \omega \in others
\end{aligned}
\right. 
\end{equation}
$$
So,
$$
\begin{aligned}
\int_{-\infty}^{\infty}{|X_c(j\omega) - X_1(j\omega)|^2}d\omega &= 2\int_{3\pi}^{5\pi}({{1\over \pi}\omega^2 - {6\over\pi}\omega + 9})d\omega \\
&= 2({1\over{3\pi}}\omega^3 - {6 \over {2\pi}} \omega^2 + 9\omega) |^{5\pi}_{3\pi} \\
&= {196\over3}\pi^2 - 60\pi
\end{aligned}
$$
Because of Parseval theorem:
$$
\int_{-\infty}^{\infty}{|x(t)|^2dt} = {1\over{2\pi}}\int_{-\infty}^{\infty}{|X(j\omega)|^2dt}
$$
So, 
$$
\int_{-\infty}^{\infty}{|x_c(t) - x_1(t)|^2dt} = {1\over{2\pi}}\int_{-\infty}^{\infty}{|X_c(j\omega) - X_1(j\omega)|^2dt} = {98\over3}\pi - 30
$$

For $x_2(t)$, the sampling interval is smaller than Nyquist interval after convert with h(t). So, the energy reduction comes from the cut of H(jw), which is shown in the graph.
![[Pasted image 20230320185840.png]]
The $|X_c(j\omega) - X_2(j\omega)|$ is shown in the graph too, which is :
$$
\begin{equation}
|X_c(j\omega) - X_2(j\omega)|  = \left\{
\begin{aligned}
{1\over \pi}\omega + 5 &, \omega \in (-5\pi, -4\pi)\\
-{1\over \pi}\omega + 5 &, \omega \in (4\pi, 5\pi)\\
0 &, \omega \in others
\end{aligned}
\right. 
\end{equation}
$$
$$
\begin{equation}
|X_c(j\omega) - X_2(j\omega)|^2  = \left\{
\begin{aligned}
{1\over \pi}\omega^2 + {10\over\pi}\omega + 25 &, \omega \in (-5\pi, -4\pi)\\
{1\over \pi}\omega^2 - {10\over\pi}\omega + 25&, \omega \in (3\pi, 5\pi)\\
0 &, \omega \in others
\end{aligned}
\right. 
\end{equation}
$$
So,
$$
\begin{aligned}
\int_{-\infty}^{\infty}{|X_c(j\omega) - X_2(j\omega)|^2}d\omega &= 2\int_{4\pi}^{5\pi}({1\over \pi}\omega^2 + {10\over\pi}\omega + 25)d\omega \\
&= 2({1\over{3\pi}}\omega^3 + {5 \over {\pi}} \omega^2 + 25\omega) |^{5\pi}_{4\pi} \\
&= 2({61\over3}\pi^2 + 70\pi)
\end{aligned}
$$
Because of Parseval theorem:
$$
\int_{-\infty}^{\infty}{|x(t)|^2dt} = {1\over{2\pi}}\int_{-\infty}^{\infty}{|X(j\omega)|^2dt}
$$
So, 
$$
\int_{-\infty}^{\infty}{|x_c(t) - x_2(t)|^2dt} = {1\over{2\pi}}\int_{-\infty}^{\infty}{|X_c(j\omega) - X_2(j\omega)|^2dt} = {61\over3}\pi + 70
$$
#### (b)
Let's say:
$$
\begin{aligned}
\begin{equation}
H(j\Omega) = \left\{
\begin{aligned}
1 &, |\Omega| < 5\pi - l \\
0 &, |\Omega| > 5\pi - l
\end{aligned}
\right.
\end{equation}
\ \ \ \ \ \ , l \in [0, \pi]
\end{aligned}
$$
The $|X_c(j\omega) - X(j\omega)|$ is basically the green shadow 
![[Pasted image 20230320193937.png]]
which is:
$$
\begin{equation}
|X_c(j\omega) - X(j\omega)|  = \left\{
\begin{aligned}
{1\over \pi}\omega + 5 &, \omega \in (-5\pi, -5\pi + l)\\
{1\over \pi}\omega - (5 -{1\over \pi}l) &, \omega \in (-5\pi + l, -3\pi - l)\\
-{1\over \pi}\omega + (5 -{1\over \pi}l)&, \omega \in (3\pi + l, 5\pi - l)\\
-{1\over \pi}\omega = 5 &, \omega \in (5\pi - l, 5\pi)\\
0 &, \omega \in others
\end{aligned}
\right. 
\end{equation}
$$
$$
\begin{equation}
|X_c(j\omega) - X(j\omega)|^2  = \left\{
\begin{aligned}
{1\over \pi}\omega^2 + {10\over\pi}\omega + 25 &, \omega \in (-5\pi, -5\pi + l)\\
{1\over \pi}\omega^2 + {2{(5 -{1\over \pi}l)}\over\pi}\omega + (5 -{1\over \pi}l)^2 &, \omega \in (-5\pi + l, -3\pi - l)\\
{1\over \pi}\omega^2 + {2{(5 -{1\over \pi}l)}\over\pi}\omega + (5 -{1\over \pi}l)^2&, \omega \in (3\pi + l, 5\pi - l)\\
{1\over \pi}\omega^2 - {10\over\pi}\omega + 25 &, \omega \in (5\pi - l, 5\pi)\\
0 &, \omega \in others
\end{aligned}
\right. 
\end{equation}
$$
So,
$$
\begin{aligned}
\int_{-\infty}^{\infty}{|X_c(j\omega) - X_2(j\omega)|^2}d\omega &= 2(\int_{5\pi-l}^{5\pi}({1\over \pi}\omega^2 + {10\over\pi}\omega + 25)d\omega +
\int^{5\pi - l}_{3\pi + l}({1\over \pi}\omega^2 + {2{(5 -{1\over \pi}l)}\over\pi}\omega + (5 -{1\over \pi}l)^2
)d\omega \\
&= a \ huge \ function \ about \ l
\end{aligned}
$$
It is not hard to see that (i.e. I am tierd of solving this intrgral equation), when $l = \pi$, the function about l will get to its minimum point. 

## 2.4 
![[Pasted image 20230320195424.png]]
#### (a)
$$
\begin{aligned}
r(t) * h(t) &= [s(t) + as(t - t_0)]*[\delta(t) - a\delta(t - t_0)] \\
&=s(t)*\delta(t) - s(t)*a\delta(t - t_0) + as(t - t_0) * \delta(t) + as(t - t_0) * a\delta(t - t_0) \\
&= s(t) - as(t-t0) + as(t-t0) - a^2s(t-2t_0) \\
&= s(t) - a^2s(t-2t_0)
\end{aligned}
$$
The multi-path phenomenon is alleviated because:
1. $0<a<1$, so $a^2 < a < 1$. The noise will be less than before
2. The noise change from $s(t-t_0)$ to $s(t-2t_0)$, which has less depentancy across time.

#### (b)
