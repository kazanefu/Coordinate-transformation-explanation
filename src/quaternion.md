# クォータニオンによる回転

## クォータニオンとは

まずはクォータニオンとは何かを簡単に説明します。

**Quaternion**を日本語にすると**四元数**といいます。四元数は複素数の拡張のようなもので虚数単位を3つ持ちます。
$$
p = w + ix + jy + kz
$$
虚数単位なのでもちろん\\(i^2 = j^2 = k^2 = -1\\)です。

異なる虚数単位の積は以下のように定義されます。
$$
ij = k\\\\
jk = i\\\\
ki = j\\\\
ji = -k\\\\
kj = -i\\\\
ik = -j\\\\
$$

見ての通り四元数では乗算に可換性がありません。

加法及び乗法の結合法則, 加法の交換法則, 分配法則が成り立ちます。

実部は\\(w\\)で虚部は\\(ix + jy + kz\\)の部分です。共役は\\(\overline{p} = w - (ix + jy + kz)\\)です。

また、\\(\overline{p} \\ \overline{q} = \overline{qp}\\)が成り立ちます。

ノルムは複素数と同様に
$$
|p| = \sqrt{p\overline{p}} = \sqrt{w^2 + x^2 + y^2 + z^2}
$$
となります。

逆元は
$$
pp^{-1} = 1 \\\\
p^{-1} = \frac{\overline{p}}{|p|^2}
$$

## 証明したいもの

先に回転がどのように表せるのか示しておく。
$$
q = \cos{\frac{\theta}{2}} + I^Tv_{axis}\sin{\frac{\theta}{2}} \\\\
p' = qp\overline{q}
$$
この式を導出したい。

## Z軸周りでの回転

まずはZ軸周りでの回転(つまりXY平面に平行な平面上での回転)を考えていきます。元の3次元座標\\((x, y, z)\\)に対応する四元数を\\(p = I^T \begin{pmatrix} x \\\\ y \\\\ z\end{pmatrix} (I = \begin{pmatrix} i \\\\ j \\\\ k\end{pmatrix})\\)として回転移動先を\\(p' = I^T \begin{pmatrix} x' \\\\ y' \\\\ z'\end{pmatrix}\\)とする。このときの\\(p, p'\\)の関係を表したい。

$$
p = ix + jy + kz = (x + ky)i + kz
$$
と変形する。

この変形から\\(x + ky\\)はただの複素数なので複素数によるXY平面での回転と同じようにして回転させられそうだ。ただし、\\(kz\\)を変化させずに行う必要がある。

$$
x + ky = \sqrt{x^2 + y^2}(\cos\alpha + k \sin\alpha) = ae^{k\alpha}
$$
と表せ
$$
p = (ae^{k\alpha})i + kz
$$
となる。

目標となる形は
$$
p' = (ae^{k(\alpha + \theta)})i + kz
$$

である。
$$
q = \cos{\theta'} + k\sin{\theta'} = e^{k\theta'}
$$
として左から掛けてみる
$$
qp = e^{k\theta'} \\{ (ae^{k\alpha})i + kz\\} = (ae^{k(\alpha + \theta')})i + e^{k\theta'}kz
\\\\
(ae^{k(\alpha + \theta')})i + e^{k\theta'}kz = i(ae^{-k(\alpha + \theta')}) + e^{k\theta'}kz \because ki = -ik
$$

これでは\\(kz\\)も変化してしまっているので、その分を補正したい。そこで\\(\overline{q}\\)を右から掛けてみる

$$
qp\overline{q} = \\{i(ae^{-k(\alpha + \theta')}) + e^{k\theta'}kz\\}e^{-k\theta'} \\\\
= i(ae^{-k(\alpha + 2\theta')}) + kz
= (ae^{k(\alpha + 2\theta')})i + kz
$$

これでZ軸周りに\\(2\theta'\\)回転したものになりました。したがって
$$
q = \cos{\frac{\theta}{2}} + k\sin{\frac{\theta}{2}} = e^{k\frac{\theta}{2}}
$$
として
$$
p' = qp\overline{q}
$$
が成り立つ。よってZ軸周りでの回転については示せた。

## 任意の軸周りの回転

以下の方針で導出します。
1. \\(v_axis = u_3\\)をZ軸に相当するようにした正規直交基底\\(\\{u_1, u_2, u_3\\}\\)を作る
2. 虚数単位\\(\begin{pmatrix} i \\\\ j \\\\ k\end{pmatrix}\\)を\\(u_1, u_2, u_3\\)の座標系に座標変換
3. その座標系でZ軸周りに回転

---
1.

直交行列\\(U\\)を定義する
$$
U = (u_1, u_2, u_3)
$$
\\(U\\)が座標変換行列である。

---
2.

簡単のために
$$
p = I^Tr, I = \begin{pmatrix} i \\\\ j \\\\ k\end{pmatrix}, r = \begin{pmatrix} x \\\\ y \\\\ z\end{pmatrix}
$$
と表す。

(1.)より座標変換は
$$
r = Ur'
$$
と表せる。

これを使ってpを表すと

$$
p = I^Tr = I^TUr'
 = (I^Tu_1, I^Tu_2, I^Tu_3)r'
 = (i' j' k')r'
 = i'x' + j'y' + k'z'
$$
また
$$
I' = \begin{pmatrix} i' \\\\ j' \\\\ k'\end{pmatrix}
$$
とする

ここで\\((i', j', k')\\)も虚数単位の性質を満たすことを証明する。


