The PDF of $n$-dim gaussian random vector is given as follow:
$$f_\overrightarrow{\mathbf{x}}(\overrightarrow{x})=\frac{1}{(2\pi)^{n/2}|\mathbf{K}|^{1/2}}exp\{-\frac{1}{2}(\overrightarrow{x}-\overrightarrow{m})^T\mathbf{K}^{-1}(\overrightarrow{x}-\overrightarrow{m})\}$$
where $\mathbf{K}$ is a covariance matrix.


- if $n=2$, then
	$$\mathbf{K} = 
	\begin{equation}
		\begin{bmatrix}
			Var(X_1) & Cov(X_1, X_2) \\
			Cov(X_2, X_1) & Var(X_2)
		\end{bmatrix}
	\end{equation}
	$$
	$$
	|\mathbf{K}|=Var(X_1)Var(X_2)-Cov(X_1, X_2)Cov(X_2, X_1)
	$$
	$$
	= \sigma_1^2\sigma_2^2 - (\rho\cdot\sigma_1\sigma_2)^2 
	$$
	$$ 
	= \sigma_1^2\sigma_2^2 (1-\rho^2)
	$$

	($\rho$: correlation coefficient b/w $X_1$ and $X_2$)



- The coefficient of $exp$ in the PDF of $n$-dim gaussian random vector is defined as follow when $n=2$:
	$$ 
	\frac{1}{(2\pi)^{n/2}|\mathbf{K}|^{1/2}} 
	= \frac{1}{2\pi\cdot|\mathbf{K}|^{1/2}}
	= \frac{1}{2\pi\cdot\sqrt{\sigma_1^2\sigma_2^2 (1-\rho^2)}}
	= \frac{1}{2\pi\cdot\sigma_1\sigma_2\sqrt{1-\rho^2}}
	$$


- The power of $exp$ is defined as follow when $n=2$:
	$$
	-\frac{1}{2}(\overrightarrow{x}-\overrightarrow{m})^T\mathbf{K}^{-1}(\overrightarrow{x}-\overrightarrow{m})
	$$
	$$=
	\frac{-1}{2\sigma_1^2\sigma_2^2 (1-\rho^2)}
	
	\begin{equation}
		\begin{bmatrix}
			x_1-\mu_1  & x_2-\mu_2 
		\end{bmatrix}
	
		\begin{bmatrix}
			\sigma_2^2 & -\rho\cdot\sigma_1\sigma_2 \\
			-\rho\cdot\sigma_1\sigma_2  & \sigma_1^2
		\end{bmatrix}
	
		\begin{bmatrix}
			x_1-\mu_1  \\
			x_2-\mu_2 
		\end{bmatrix}
	\end{equation}
	$$
	$$=
	\frac{-1}{2\sigma_1^2\sigma_2^2 (1-\rho^2)}
	
	\begin{equation}
		\begin{bmatrix}
			\sigma_2^2(x_1-\mu_1)  -\rho\cdot\sigma_1\sigma_2(x_2-\mu_2) &
			-\rho\cdot\sigma_1\sigma_2(x_1-\mu_1) - \sigma_1^2(x_2-\mu_2)
		\end{bmatrix}
	
		\begin{bmatrix}
			x_1-\mu_1  \\
			x_2-\mu_2 
		\end{bmatrix}
	\end{equation}
	
	$$
	$$=
	\frac{-1}{2(1-\rho^2)}
	
	\begin{equation}
		\begin{bmatrix}
			\frac{x_1-\mu_1}{\sigma_1^2}  -\rho\cdot\frac{x_2-\mu_2}{\sigma_1\sigma_2} &
			-\rho\cdot\frac{x_1-\mu_1}{\sigma_1\sigma_2} - \frac{x_2-\mu_2}{\sigma_2^2}
		\end{bmatrix}
	
		\begin{bmatrix}
			x_1-\mu_1  \\
			x_2-\mu_2 
		\end{bmatrix}
	\end{equation}
	
	$$
	$$=
	\frac{-1}{2(1-\rho^2)}
	(T_1^2-2\rho T_1T_2 + T_2^2)
	$$
	$$(T_1=\frac{x_1-\mu_1}{\sigma_1}\; and \;T_2=\frac{x_2-\mu_2}{\sigma_2}) $$

- As a result, the PDF of 2-dim joint gaussian random variables is given:
	$$f_\overrightarrow{\mathbf{x}}(\overrightarrow{x})=\frac{1}{(2\pi)^{n/2}|\mathbf{K}|^{1/2}}exp\{-\frac{1}{2}(\overrightarrow{x}-\overrightarrow{m})^T\mathbf{K}^{-1}(\overrightarrow{x}-\overrightarrow{m})\}$$
	$$=\frac{1}{2\pi\cdot\sigma_1\sigma_2\sqrt{1-\rho^2}}exp\{\frac{-1}{2(1-\rho^2)}(T_1^2-2\rho T_1T_2 + T_2^2)\}$$
	$$(T_1=\frac{x_1-\mu_1}{\sigma_1}\; and \;T_2=\frac{x_2-\mu_2}{\sigma_2}) $$
