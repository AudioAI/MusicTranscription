$X_{r,n,m} = {\Sigma}^K_{k=1}G_{r,k} {\cdot} A_{n,k} {\cdot} S_{m,k}$  

 - 随机初始化$B,G$  
     
     - $B$迭代：${B {\lArr} B.{\times} {\frac{{\frac{X}{BG}}G^T}{1G^T}}}$  
     - $G$迭代：${G {\lArr} G.{\times} {\frac{{\nabla}{c^-}(B,G)}{{\nabla}{c^+}(B,G)}}}$，其中：${{\nabla}{c^+}(B,G)} = {B^T}1$，${{\nabla}{c^-}(B,G)} = {B^T{\frac{X}{BG}}}$
     - Cost function: ${c(B,G)=c_r(B,G)+{\alpha}c_t(G)+{\beta}c_s(G)}$  
        - $c_r(B,G)$: Reconstruction error term, using: 
	     ${D(X||BG)={\Sigma}_{k,t}[X]_{k,t}{\log}{\frac{[X]_{k,t}}{[BG]_{k,t}}}-[X]_{k,t} + [BG]_{k,t}}$  
		  
		  $c_t(G)$: Temporal continuity term, using: 
		  ${c_t(G)={\Sigma}_{j=1}^{J}{\frac{1}{{\sigma}_j^2}}{\Sigma}_{t=2}^T(g_{t,j}-g_(t-1,j)^2}, {\sigma}_j={\sqrt{(1/T){\Sigma}_{t=1}^{T}g_{t,j}^2}}$  
		- $c_s(G)$: Sparseness Objective term, using: 
		  $c_s(G) = {\Sigma}_{j=1}^J{\Sigma}_{t=1}^Tf(g_{j,t}/{\sigma}_j)$  

**I. 预处理：降采样与降噪**  
- 每8帧取平均值  
- 降噪函数，设置阈值$\sigma$： 
  $$ f(x) = \begin{cases}
  x & x>{\sigma} \\
  0 & x<={\sigma}
  \end{cases}$$  

**II. 距离向量计算** 
采用欧式距离：  
${\Sigma_i{{(x[i]-y[i])}}^2}$  

**III. 光滑处理**  

> Demo中添加光滑处理仅减小了微小扰动，线性降低了时间复杂度，但却让最后一步局部峰值提取的阈值难以界定，该步骤是否需要保留仍待实验  

**IV. 移动窗口归一化** 
本步处理方便峰值提取时设定全局阈值。  

- ${onset2(n) ={\frac{onset1(n)}{||x||_2}}}$, ${||x||_2 = ({\Sigma}_{i=1}^nx_i^2)^{\frac{1}{2}}}$  
- $x = (x_i, x_{i+1}, ..., x_{i+hop+overlap})^T, i = 0, 1, ...$  
- Window size $i+hop+overlap$ is the sample rate of $onset1$  

**V. 局部峰值提取**  
设定阈值设置阈值$\sigma$：  
  $$ f(x) = \begin{cases}
  1 & x>{\sigma} \\
  0 & x<={\sigma}
  \end{cases}$$  