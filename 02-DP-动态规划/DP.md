# 强化学习-动态规划-DP

这里讲动态规划，主要是用动态规划来解决MDP的中最优的策略，并得出最优的价值函数。这节主要介绍两中动态规划的方法，一个是**策略迭代**方法，另一个是**价值迭代**方法。

## 策略迭代

策略迭代主要分两个，一个是**策略评估**，一个是**策略改进**。定义比较简单，分别如下：

* **策略评估**：给定任意的一个 $\pi$ ，求出 状态价值函数 $v_\pi(s)$ 或者状态动作价值函数 $q_\pi(s,a)$。
* **策略改进**：依据策略评估得出来的 $v_\pi(s)$ 或者 $q_\pi(s,a)$，从其中构造出一个新的 $\pi'$ ，使得 $\pi' \geq \pi $ ，这样就完成了一次迭代。
  * 换句话说，就是在每个状态下根据$v_\pi(s)$ 或者 $q_\pi(s,a)$选择一个最优的策略，这个策略就是 $\pi'$ 。
  * 这种根据`原策略的价值函数`执行贪心算法，来构造一个更好策略的过程，我们称为策略改进。
* **策略迭代**： 就是递归以上两个过程，以上面的例子，得到$\pi'$的价值函数，然后再以$\pi'$的价值函数构造一个新的$\pi''$ ，不断迭代。

### 策略评估-解析解

策略评估，就是已知 MDP，“已知MDP”的意思是知道其动态特性，也就是 $p(s',r \mid s,a)$，给定$\pi$ ，求$v_\pi(s)$。

已知$(\forall s\in S)$ ，即有多少个$s$ 就会有多少$v_\pi(s)$，可得列向量如下：
$$
\large
v_\pi(s)= \begin{pmatrix}
v_\pi(s_1)\\
v_\pi(s_2)\\
\vdots    \\
v_\pi(s_{\lvert S \rvert})

\end{pmatrix} _{\lvert S \rvert \times 1}
$$



上节课我们得到价值函数的公式，并进一步推导得到贝尔曼期望方程：
$$
\large
{\begin{align}  
v_\pi(s) =& E_\pi[G_t \mid S_t = s] \\
=& E_\pi[R_{t+1} + \gamma v_\pi(S_{t+1} \mid S_t=s)]\\
=& v_\pi(s) = \sum_{a\in A} \pi(a\mid s) \sum_{s',r}p(s',r \mid s,a)[r+ \gamma v_\pi(s')] 
\end{align}
}
$$
从上面的列向量中得知，如果我们想要求整个的$v_\pi(s)$ ，就是把$v_\pi(s_1),v_\pi(s_2),v_\pi(s_3)...$ 等所有的价值函数都要求出来。我们这节的目的就是把公式中的累加符号去掉，得到另一个更为简单的式子。接下来我们对下面的式子进行化简如下：
$$
\large{
\begin{align} 

v_\pi(s) =& \sum_{a\in A} \pi(a\mid s) \sum_{s',r}p(s',r \mid s,a)[r+ \gamma v_\pi(s')]\\
= & \underbrace {\sum_{a\in A} \pi(a\mid s) \sum_{s',r}p(s',r \mid s,a) \cdot r }_{①}
+ 
\underbrace{\gamma \sum_{a\in A} \pi(a\mid s) \sum_{s',r}p(s',r \mid s,a) \cdot v_\pi(s')}_{②}


\end{align}
}
$$
**分解①式**：对于①式而言，$s'$ 只出现在函数$p(s',r \mid s,a)$ 中，是可以积分积掉的。故由①得：
$$
\large{
\begin{align}

\sum_{a\in A} \pi(a\mid s) \sum_{s',r}p(s',r \mid s,a) \cdot r  \\
=& \sum_{a\in A}  \pi(a\mid s) \underbrace {\sum_{r}  r\cdot p(r \mid s,a)}_{E_\pi[R_{t+1}  \mid S_t=s,A_t=a]} \quad\quad (1)  \\


\end{align}
}
$$
（1）式正好是期望，此时我们也定义一个函数（记号） $r(s,a) \dot{=} E_\pi[R_{t+1}  \mid S_t=s,A_t=a]$ 。表示$s,a$ 这个二元组的收益。故我们的推导变成如下：
$$
\large{
\begin{align}

\sum_{a\in A} \pi(a\mid s) \sum_{s',r}p(s',r \mid s,a) \cdot r  \\
=& \sum_{a\in A} \pi(a\mid s) \underbrace {\sum_{r}  r\cdot p(r \mid s,a)}_{r(s,a) \dot{=}E_\pi[R_{t+1}  \mid S_t=s,A_t=a]} \quad\quad (1)  \\
=& \sum_{a\in A} \pi(a\mid s) \cdot r(s,a) \quad\quad\quad\quad \quad\quad \quad\quad\ (2)



\end{align}
}
$$
（2）式中，如果把 $a$ 都积分掉，那么（2）式就和$a$ 就没关系了，只和$s$ 有关，这里我们再次引入一个记号$r_\pi(s)$用来表示这个新的关系如下：
$$
\large{
\begin{align}

\sum_{a\in A} \pi(a\mid s) \sum_{s',r}p(s',r \mid s,a) \cdot r  \\
=& \sum_{a\in A} \pi(a\mid s) \underbrace {\sum_{r}  r\cdot p(r \mid s,a)}_{r(s,a) \dot{=}E_\pi[R_{t+1}  \mid S_t=s,A_t=a]} \quad\quad (1)  \\
=& \sum_{a\in A} \pi(a\mid s) \cdot r(s,a) \quad\quad\quad\quad \quad\quad \quad\quad\ (2) \\
\dot{=}&r_\pi(s)  \quad\quad\quad\quad \quad\quad \quad\quad\quad \quad\quad\ \quad\quad \quad\quad\ (3)



\end{align}
}
$$
此时，①式化简至此，需要提到的一点是，$r_\pi(s)$ 应该由多少个呢？在一开始我们就提到过 “$S$中有多少个$s$ 就会有多少$v_\pi(s)$，” ，显然 $r_\pi(s)$ 的数量也是 $\lvert S \rvert$ 个。故可得列向量如下:
$$
\large
r_\pi(s)= \begin{pmatrix}
r_\pi(s_1)\\
r_\pi(s_2)\\
\vdots    \\
r_\pi(s_{\lvert S \rvert})

\end{pmatrix} _{\lvert S \rvert \times 1}
$$
**分解②式**：在 $p(s',r \mid s,a) \cdot v_\pi(s')$ 中，我们发现 $s'$ 在式子中都存在，但是$r$ 只存在于函数 $p(s',r \mid s,a)$ 中，故积分可以积掉，则②式可以进一步写成：
$$
\large{
\begin{align}

\gamma \sum_{a\in A} \pi(a\mid s) \sum_{s',r}p(s',r \mid s,a) \cdot v_\pi(s') \\
=& \gamma \sum_{a\in A} \pi(a\mid s) \sum_{s'}\underbrace{p(s' \mid s,a)}_{状态转移函数} \cdot v_\pi(s') \quad\quad (4)


\end{align}
}
$$
在（4）式中，可以发现在两个累加号中都有 $a$（注：虽然也都有 $s$，但是累加号下面是$a$ ，故积分只能积掉 $a$），积分可以积掉，但是一定和 $s,s'$ 有关，故引出一个记号 $P_\pi(s,s')$ 来表示。 故继续推导如下：
$$
\large{
\begin{align}

\gamma \sum_{a\in A} \pi(a\mid s) \sum_{s',r}p(s',r \mid s,a) \cdot v_\pi(s') \\
=& \gamma \sum_{a\in A} \pi(a\mid s) \sum_{s'}\underbrace{p(s' \mid s,a)}_{状态转移函数} \cdot v_\pi(s') \quad\quad (4) \\


=& \gamma \sum_{s'} \underbrace{\sum_{a\in A} \pi(a\mid s) p(s' \mid s,a)}_{P_\pi(s,s')} \cdot v_\pi(s') \quad\quad (5) \\

=&  \gamma \sum_{s'} P_\pi(s,s') \cdot v_\pi(s') \quad\quad (6)\\


\end{align}
}
$$
由（6）式可知，对于 $P_\pi(s,s')$ 来说，一共由多少个值呢？很显然，$s$ 和 $s'$ 分别由 $\lvert S \rvert $ 个值，故 $P_\pi(s,s')$ 会有 $\lvert S \rvert \times \lvert S \rvert  $  个。故可得矩阵如下：
$$
\large
P_\pi(s,s')= \begin{pmatrix}
P_\pi(s_1,s'_1) & \cdots & P_\pi(s_1,s'_{\lvert S \rvert})\\
\vdots & \ddots & \vdots \\
P_\pi(s_{\lvert S \rvert},s'_1) & \cdots & P_\pi(s_{\lvert S \rvert},s'_{\lvert S \rvert})\\

\end{pmatrix} _{\lvert S \rvert \times \lvert S \rvert}
$$
于是，结合（3）和（6）式得：
$$
\large{
v_\pi(s) = r_\pi(s) + \gamma \sum_{s'} P_\pi(s,s') \cdot v_\pi(s')
}
$$
令$s_i$

## 价值迭代



## 参考文献

https://www.cnblogs.com/pinard/p/9463815.html