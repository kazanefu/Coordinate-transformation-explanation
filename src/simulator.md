# 再現

ここまでで数学的な内容はわかったのでコードを書いていきましょう

## Vector3

まず`Vector3`の代わりに`Vec3`という構造体を書きます。この構造体は3次元ベクトルを表現し`x, y , z`の`float`型のフィールドを持ちます。3次元ベクトル必要なメモリ領域も少なく固定で数値のように値渡しで扱いたいので`class`ではなく`struct`にします。実際の`Vector3`にはもっといろいろなメソッドがありますが今回必要な分だけ実装します。これに関してはただの3次元ベクトルなのでとくに触れることもないですね。
```cs
using Unity.VisualScripting;
using UnityEngine;

[System.Serializable]
public struct Vec3
{
    public float x;
    public float y;
    public float z;
    public Vec3(float x,float y,float z)
    {
        this.x = x;
        this.y = y;
        this.z = z;
    }
    public Vector3 ToVector3() 
    {
        return new Vector3(x, y, z);
    }

    public float Length()
    {
        return Mathf.Sqrt(x * x + y * y + z * z);
    }

    public static Vec3 FromVector3(Vector3 vector3)
    {
        return new Vec3(vector3.x, vector3.y, vector3.z);
    }

    // スカラー倍
    public static Vec3 operator *(Vec3 v, float s)
    {
        return new Vec3(v.x * s, v.y * s, v.z * s);
    }
    public static Vec3 operator *(float s, Vec3 v)
    {
        return new Vec3(v.x * s, v.y * s, v.z * s);
    }
    public static Vec3 operator /(Vec3 v, float s)
    {
        return new Vec3(v.x / s, v.y / s, v.z / s);
    }

    public Vec3 Normalized()
    {
        float len = this.Length();
        return this / len;
    }
}
```

## Quaternion

`Quaternion`を再現する`Quat`を作ります。これも特に言うことはありませんがあえて言うなら基本的に正規化した状態で扱います。
```cs
using System.Data;
using UnityEngine;

[System.Serializable]
public struct Quat
{
    public float w;
    public float x;
    public float y;
    public float z;
    
    public Quat(float w,float x, float y, float z)
    {
        this.w = w;
        this.x = x;
        this.y = y;
        this.z = z;
    }
    public static Quat FromAxisAngle(Vec3 axis, float angleDeg)
    {
        // 軸は正規化必須
        Vec3 n = axis.Normalized();

        float rad = angleDeg * Mathf.Deg2Rad;
        float half = rad * 0.5f;

        float s = Mathf.Sin(half);
        float c = Mathf.Cos(half);

        return new Quat(
            c,          // w
            n.x * s,    // x
            n.y * s,    // y
            n.z * s     // z
        );
    }
    public static Quat FromQuaternion(Quaternion quaternion)
    {
        quaternion.Normalize();
        return new Quat(quaternion.w, quaternion.x, quaternion.y, quaternion.z);
    }
    public Quaternion ToQuaternion()
    {
        Nomalise();
        return new Quaternion(x, y, z, w);
    }
    public static Quat FromRotationMatrix(Vec3 c0, Vec3 c1, Vec3 c2)
    {
        float m00 = c0.x, m01 = c1.x, m02 = c2.x;
        float m10 = c0.y, m11 = c1.y, m12 = c2.y;
        float m20 = c0.z, m21 = c1.z, m22 = c2.z;

        float trace = m00 + m11 + m22;

        if (trace > 0f)
        {
            float s = Mathf.Sqrt(trace + 1f) * 2f;
            return new Quat(
                0.25f * s,
                (m21 - m12) / s,
                (m02 - m20) / s,
                (m10 - m01) / s
            );
        }
        else if (m00 > m11 && m00 > m22)
        {
            float s = Mathf.Sqrt(1f + m00 - m11 - m22) * 2f;
            return new Quat(
                (m21 - m12) / s,
                0.25f * s,
                (m01 + m10) / s,
                (m02 + m20) / s
            );
        }
        else if (m11 > m22)
        {
            float s = Mathf.Sqrt(1f + m11 - m00 - m22) * 2f;
            return new Quat(
                (m02 - m20) / s,
                (m01 + m10) / s,
                0.25f * s,
                (m12 + m21) / s
            );
        }
        else
        {
            float s = Mathf.Sqrt(1f + m22 - m00 - m11) * 2f;
            return new Quat(
                (m10 - m01) / s,
                (m02 + m20) / s,
                (m12 + m21) / s,
                0.25f * s
            );
        }
    }

    // オイラー角との変換は一意に定まらないのでUnityの実装と完全に合わせるためにUnityのをそのまま使う
    public static Quat FromEuler(Vec3 euler)
    {
        Quaternion q = Quaternion.Euler(euler.ToVector3());
        return FromQuaternion(q);
    }
    public Vec3 ToEuler()
    {
        Quaternion q = ToQuaternion();
        Vector3 v = q.eulerAngles;
        return Vec3.FromVector3(v);
    }
    public static Quat operator *(Quat a, Quat b)
    {
        return new Quat(
            a.w * b.w - a.x * b.x - a.y * b.y - a.z * b.z,
            a.w * b.x + a.x * b.w + a.y * b.z - a.z * b.y,
            a.w * b.y - a.x * b.z + a.y * b.w + a.z * b.x,
            a.w * b.z + a.x * b.y - a.y * b.x + a.z * b.w
        );
    }
    public static Vec3 operator *(Quat q, Vec3 v)
    {
        // v' = q * v * q^-1
        Quat qv = new Quat(0f, v.x, v.y, v.z);
        Quat inv = q.Normalized().Conjugate();

        Quat result = q * qv * inv;

        return new Vec3(result.x, result.y, result.z);
    }
    public Quat Conjugate()
    {
        return new Quat(w, -x, -y, -z);
    }
    public Quat Normalized()
    {
        float len = Mathf.Sqrt(w * w + x * x + y * y + z * z);
        return new Quat(w / len, x / len, y / len, z / len);
    }
    public void Nomalise()
    {
        Quat nomalized = Normalized();
        w = nomalized.w;
        x = nomalized.x;
        y = nomalized.y;
        z = nomalized.z;
    }
}
```


## Matrix4x4

乗算の演算子オーバーロードとトランスフォームとの相互変換のためのメソッドがあるだけのただの4x4行列ですね
```cs
using UnityEngine;
using UnityEngine.UIElements;

[System.Serializable]
public struct Mat4
{
    // 4x4行列
    public float[,] matrix; 

    public Mat4(float[,] matrix)
    {
        this.matrix = matrix;
    }

    // 単位行列
    public static Mat4 Identity()
    {
        float[,] matrix = new float[4, 4] {
            {1f,0f,0f,0f },
            {0f,1f,0f,0f },
            {0f,0f,1f,0f },
            {0f,0f,0f,1f },
        };
        return new Mat4(matrix);
    }

    // 零行列
    public static Mat4 Zero()
    {
        float[,] matrix = new float[4, 4] {
            {0f,0f,0f,0f },
            {0f,0f,0f,0f },
            {0f,0f,0f,0f },
            {0f,0f,0f,0f },
        };
        return new Mat4(matrix);
    }

    // 平行移動から
    public static Mat4 FromTranslation(Vec3 vec3)
    {
        // matrix[i,j] のj == 3
        Mat4 mat4 = Mat4.Identity();
        mat4.matrix[0, 3] = vec3.x;
        mat4.matrix[1, 3] = vec3.y;
        mat4.matrix[2, 3] = vec3.z;
        return mat4;
    }
    // 平行移動成分
    public Vec3 GetTranslation()
    {
        return new Vec3(matrix[0,3], matrix[1, 3], matrix[2, 3]);
    }
    // 大きさから
    public static Mat4 FromScale(Vec3 vec3)
    {
        // 0..3の対角
        Mat4 mat4 = Mat4.Identity();
        mat4.matrix[0, 0] = vec3.x;
        mat4.matrix[1, 1] = vec3.y;
        mat4.matrix[2, 2] = vec3.z;
        return mat4;
    }
    public Vec3 GetScale()
    {
        Vec3 col0 = new Vec3(matrix[0, 0], matrix[1, 0], matrix[2, 0]);
        Vec3 col1 = new Vec3(matrix[0, 1], matrix[1, 1], matrix[2, 1]);
        Vec3 col2 = new Vec3(matrix[0, 2], matrix[1, 2], matrix[2, 2]);

        float sx = col0.Length();
        float sy = col1.Length();
        float sz = col2.Length();

        return new Vec3(sx, sy, sz);
    }
    // p' = q * p * q^-1と同等になるような回転行列
    // この変換は準同型写像
    public static Mat4 FromRotation(Quat quat)
    {
        var (w, x, y, z) = (quat.w, quat.x, quat.y, quat.z);
        float[,] matrix = new float[4, 4] {
            {1f - 2f * (y * y + z * z),2f * (x * y - z * w),2f * (x * z + y * w),0f },
            {2f * (x * y + z * w),1f - 2f * (x * x + z * z),2f * (y * z - x * w),0f },
            {2f * (x * z - y * w),2f * (y * z + x * w),1f - 2f * (x * x + y * y),0f },
            {0f,0f,0f,1f },
        };
        return new Mat4(matrix);
    }
    public Quat GetRotation()
    {
        // --- scale ---
        Vec3 col0 = new Vec3(matrix[0, 0], matrix[1, 0], matrix[2, 0]);
        Vec3 col1 = new Vec3(matrix[0, 1], matrix[1, 1], matrix[2, 1]);
        Vec3 col2 = new Vec3(matrix[0, 2], matrix[1, 2], matrix[2, 2]);

        float sx = col0.Length();
        float sy = col1.Length();
        float sz = col2.Length();

        // --- rotation（スケール除去）---
        if (sx != 0f) col0 = col0 / sx;
        if (sy != 0f) col1 = col1 / sy;
        if (sz != 0f) col2 = col2 / sz;

        // 回転行列 → Quaternion
        return Quat.FromRotationMatrix(
            col0, col1, col2
        );
    }
    // 行列の乗算
    public static Mat4 operator *(Mat4 lhs, Mat4 rhs)
    {
        Mat4 result = Mat4.Zero();
        for(int i = 0;i < 4; i++)
        {
            for (int j = 0; j < 4; j++)
            {
                for (int k = 0; k < 4; k++)
                {
                    result.matrix[i, j] += lhs.matrix[i, k] * rhs.matrix[k, j];
                }
            }
        }
        return result;
    }
    public static Vec3 operator *(Mat4 lhs, Vec3 rhs)
    {
        float x = rhs.x;
        float y = rhs.y;
        float z = rhs.z;
        float w = 1f;

        float rx =
            lhs.matrix[0, 0] * x +
            lhs.matrix[0, 1] * y +
            lhs.matrix[0, 2] * z +
            lhs.matrix[0, 3] * w;

        float ry =
            lhs.matrix[1, 0] * x +
            lhs.matrix[1, 1] * y +
            lhs.matrix[1, 2] * z +
            lhs.matrix[1, 3] * w;

        float rz =
            lhs.matrix[2, 0] * x +
            lhs.matrix[2, 1] * y +
            lhs.matrix[2, 2] * z +
            lhs.matrix[2, 3] * w;

        float rw =
            lhs.matrix[3, 0] * x +
            lhs.matrix[3, 1] * y +
            lhs.matrix[3, 2] * z +
            lhs.matrix[3, 3] * w;

        // wで割る（通常のアフィン変換なら rw = 1 なのでそのまま）
        if (rw != 0f)
        {
            rx /= rw;
            ry /= rw;
            rz /= rw;
        }

        return new Vec3(rx, ry, rz);
    }
}
```

## MyTransform

これは単にUnityのTransformとの変換のためのものです
```cs
using UnityEngine;

public class MyTransform
{
    public Vec3 position;
    public Quat rotation;
    public Vec3 scale;
    public Mat4 ToMat4()
    {
        Mat4 pos = Mat4.FromTranslation(position);
        Mat4 rot = Mat4.FromRotation(rotation);
        Mat4 sca = Mat4.FromScale(scale);
        return pos * rot * sca;
    }
    public void FromMat4(Mat4 m)
    {
        position = m.GetTranslation();
        scale = m.GetScale();
        rotation = m.GetRotation();
    } 
    public MyTransform(Transform transform)
    {
        position = Vec3.FromVector3(transform.position);
        rotation = Quat.FromQuaternion(transform.rotation);
        scale = Vec3.FromVector3(transform.localScale);
    }
    public void SyncTransform(Transform transform)
    {
        transform.position = position.ToVector3();
        transform.rotation = rotation.ToQuaternion();
        transform.localScale = scale.ToVector3();
    }
}
```

## SyncTransform

これはUnityのTransformと同期させたりMyTransformの方から動かしたりするものですね
```cs
using UnityEngine;

public class SyncTransform : MonoBehaviour
{
    [SerializeField] private MyTransform globalTransform;
    [SerializeField] private MyTransform myTransform;
    [SerializeField] private Transform parent;
    [SerializeField] private MyTransform parentMyTransform;
    [SerializeField] private Vec3 position;
    [SerializeField] private Quat rotation;
    [SerializeField] private Vec3 euler;
    [SerializeField] private Vec3 scale;
    [SerializeField] private Vec3 axis;
    [SerializeField] private float angle;
    [SerializeField] private Vec3 parentPosition;
    [SerializeField] private Quat parentRotation;
    [SerializeField] private Vec3 parentEuler;
    [SerializeField] private Vec3 parentScale;
    // Start is called once before the first execution of Update after the MonoBehaviour is created
    void Start()
    {
        globalTransform = new MyTransform(transform);
        parentMyTransform = new MyTransform(parent);
        parentPosition = parentMyTransform.position;
        parentRotation = parentMyTransform.rotation;
        parentEuler = parentRotation.ToEuler();
        parentScale = parentMyTransform.scale;
        myTransform = new MyTransform(transform);
        position = myTransform.position;
        rotation = myTransform.rotation;
        scale = myTransform.scale;
    }

    // Update is called once per frame
    void Update()
    {
        // クォータニオンによる回転
        // ワールド座標系で
        // rotation = Quat.FromAxisAngle(axis,angle * Time.deltaTime) * rotation;
        // ローカル座標系で
        rotation = rotation * Quat.FromAxisAngle(axis, angle * Time.deltaTime);

        myTransform.position = position;
        myTransform.rotation = rotation;
        euler = rotation.ToEuler();
        myTransform.scale = scale;

        parentRotation = Quat.FromEuler(parentEuler);
        parentMyTransform.position = parentPosition;
        parentMyTransform.rotation = parentRotation;
        parentMyTransform.scale = parentScale;

        // globalTransformを親の4x4行列とローカルの4x4行列の積で求める

        // 自身の位置を決めてから親からの相対位置に持っていく
        // 親子関係はこれ
        globalTransform.FromMat4(parentMyTransform.ToMat4() * myTransform.ToMat4());
        // 親の位置から自分のトランスフォーム分変化させる
        // globalTransform.FromMat4(myTransform.ToMat4() * parentMyTransform.ToMat4());

        // UnityのTransformに同期
        globalTransform.SyncTransform(transform);
        parentMyTransform.SyncTransform(parent);
    }
}
```