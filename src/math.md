# 基本的な数学の確認

多分大丈夫だと思いますが公式とかを一応少し復習しましょう。

---
**オイラーの公式**

$$
re^{i\theta} = r(\cos\theta + i \sin\theta )
$$

---
**行列の積**

行列
$$
A = (a_{ik}) \in \mathbb{R}^{m \times n}, \quad
B = (b_{kj}) \in \mathbb{R}^{n \times p}
$$
を考える。

このとき、行列積 \\(C = AB\\) は \\(m \times p\\) 行列であり、
その \\((i,j)\\) 成分は次で与えられる：

$$
c_{ij} = \sum_{k=1}^{n} a_{ik} b_{kj}
$$

すなわち、\\(A\\) の第 \\(i\\) 行と \\(B\\) の第 \\(j\\) 列の内積である。

---

**2次元での平行移動と回転移動**

複素数を用いた場合:

$$
p' = e^{i\theta} s p + t
$$

のように \\( s \in \mathbb{R} \\) を拡大縮小、\\( \theta \in \mathbb{R} \\) を回転、\\( t \in \mathbb{C} \\) を平行移動として表せます。

これを一つの行列の積にまとめると、複素数 \\( p = x + iy \\) をベクトル
\\[
\begin{pmatrix}
x \\\\
y
\end{pmatrix}
\\]
とみなすことで、次のように書き換えられます：

$$
\begin{pmatrix}
x' \\\\
y'
\end{pmatrix} =
s
\begin{pmatrix}
\cos \theta & -\sin \theta \\\\
\sin \theta & \cos \theta
\end{pmatrix}
\begin{pmatrix}
x \\\\
y
\end{pmatrix}
+
\begin{pmatrix}
t_x \\\\
t_y
\end{pmatrix}
$$

ここで \\( t = t_x + i t_y \\) です。

---

しかしこの形では、回転・拡大と平行移動が「行列 + ベクトル」として分かれており、
**1つの行列の積としてまとめることができません。**

そこで、同次座標を導入します。

$$
\begin{pmatrix}
x \\\\
y \\\\
1
\end{pmatrix}
$$

と拡張すると、次のように1つの行列で表現できます：

$$
\begin{pmatrix}
x' \\\\
y' \\\\
1
\end{pmatrix} =
\begin{pmatrix}
s\cos \theta & -s\sin \theta & t_x \\\\
s\sin \theta & s\cos \theta  & t_y \\\\
0            & 0             & 1
\end{pmatrix}
\begin{pmatrix}
x \\\\
y \\\\
1
\end{pmatrix}
$$

この変換行列は、拡大縮小・回転・平行移動の3つの変換に分解でき、
それらの積として表すこともできます。

$$
\begin{pmatrix}
s\cos \theta & -s\sin \theta & t_x \\\\
s\sin \theta & s\cos \theta  & t_y \\\\
0            & 0             & 1
\end{pmatrix} =
\begin{pmatrix}
1 & 0 & t_x \\\\
0 & 1 & t_y \\\\
0 & 0 & 1
\end{pmatrix}
\begin{pmatrix}
\cos \theta & -\sin \theta & 0 \\\\
\sin \theta & \cos \theta  & 0 \\\\
0           & 0            & 1
\end{pmatrix}
\begin{pmatrix}
s & 0 & 0 \\\\
0 & s & 0 \\\\
0 & 0 & 1
\end{pmatrix}
$$

右から順に、

- 拡大縮小
- 回転
- 平行移動

を表しています。

このように、複数の変換を**行列の積として合成できる**ことが重要です。

---

**正規直交基底**

ベクトル空間\\(V = \mathbb{R}^n\\)は\\(\mathbb{R}\\)上の内積空間として、\\(a,b \in V\\)で標準内積を\\((a, b)\\)と表記し\\((a, b) \in \mathbb{R}\\)である。

このとき次の条件を満たす\\(V\\)の基\\(\\{ u_1, ..., u_n\\}\\)を正規直交基底という
$$
(u_i, u_j) = \delta_{ij}
$$
(\\(\delta_{ij}\\)はクロネッカーのデルタ)
