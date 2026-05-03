# 4x4行列で3次元座標を扱う


2次元の
$$
p' = e^{i\theta} s p + t
$$
と同様に四元数の範囲で3次元は
$$
p' = r(sp)\overline{r} + t
$$
のように拡大縮小,回転,平行移動ができることがわかりました。しかしこれでは複数種類の演算が出てきて不便が生じることがあります。なので3次元でも2次元の
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
のように同次座標を導入して統一的な表現・計算で表したいです。

$$
\begin{pmatrix}
x \\\\
y \\\\
z \\\\
1
\end{pmatrix}
$$

## 拡大縮小
$$
\begin{pmatrix}
x' \\\\
y' \\\\
z' \\\\
1
\end{pmatrix} =
\begin{pmatrix}
s_x & 0 & 0 & 0 \\\\
0 & s_y & 0 & 0 \\\\
0 & 0 & s_z & 0 \\\\
0 & 0 & 0 & 1
\end{pmatrix}
\begin{pmatrix}
x \\\\
y \\\\
z \\\\
1
\end{pmatrix}
$$

## 平行移動
$$
\begin{pmatrix}
x' \\\\
y' \\\\
z' \\\\
1
\end{pmatrix} =
\begin{pmatrix}
1 & 0 & 0 & t_x \\\\
0 & 1 & 0 & t_y \\\\
0 & 0 & 1 & t_z \\\\
0 & 0 & 0 & 1
\end{pmatrix}
\begin{pmatrix}
x \\\\
y \\\\
z \\\\
1
\end{pmatrix}
$$

## 回転移動

\\(q = w + iv_x + jv_y + kv_z\\)に対して
$$
\begin{pmatrix}
x' \\\\
y' \\\\
z' \\\\
1
\end{pmatrix} =
\begin{pmatrix}
1 - 2(y^2 + z^2) & 2(xy - zw) & 2(xz + yw) & 0 \\\\
2(xy + zw) & 1 - 2(x^2 + z^2) & 2(yz - xw) & 0 \\\\
2(xz - yw) & 2(yz + xw) & 1 - 2(x^2 + y^2) & 0 \\\\
0 & 0 & 0 & 1
\end{pmatrix}
\begin{pmatrix}
x \\\\
y \\\\
z \\\\
1
\end{pmatrix}
$$

---

Unityではこれらを掛けた`TRS`行列と呼ばれる`position(translation) / rotation / scale`を一つの4x4行列にまとめています。この4x4行列の型は`Matrix4x4`として存在しています。

内部的には
$$
M = TRS
$$
$$
p' = Mp = (TRS)p = (T(R(Sp)))
$$
のようになっていて`S -> R -> T`の順で作用しているのと等しいです。

## どんなところで使われているのか

- ワールド座標のMatrix4x4 = ローカル座標のMatrix4x4 * 親のワールド座標のMatrix4x4でのローカル->ワールドの変換
- ワールド座標 -> カメラ座標への変換
- 描画

逆に単に回転だけ,平行移動だけの変換しかしない場合はQuaternion,Vector3でそれぞれ計算したほうが無駄が少ないのでMatrix4x4はあまり使いません。