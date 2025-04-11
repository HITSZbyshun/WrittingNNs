## FP16

16 位

sign (1bit)  exponent(5bit)  fraction(10bit)

### sign

0 为正

1为负

### exponent

决定大区间

#### 11111、00000记为特殊符号


| Binary             | Value                                                       | Notes                              |
| ------------------ | ----------------------------------------------------------- | ---------------------------------- |
| 0 00000 0000000000 | 0                                                           |                                    |
| 0 00000 0000000001 | $2^{-14}\times(0+\dfrac{1}{1024})\approx 0.000000059604645$ | smallest positive subnormal number |
| 0 00000 1111111111 | $2^{-14}\times(0+\dfrac{1023}{1024})\approx 0.000060975552$ | largest subnormal number           |
| 0 11111 0000000000 | $\infty$                                                    | infinity                           |
| 1 11111 0000000000 | - $\infty$                                                  | negative infinity                  |



#### 00001(1)~11110(30)   

$$
2^{-14}\sim2^{15}
$$

#### 最大正数（上溢出）

$$
0\quad11111\quad1111111111 = 2^{15}\times(1+\dfrac{1023}{1024}) = 65504
$$

65520 是float16 从65504跳到inf的临界值

假设临界值是$y_c$，则
$$
y_c=2^{15}\times(1+\dfrac{x_c}{1024}) \\
x_c=1023.5 \\
y_c=65520
$$
65504 是精度未折损的最大正数

真溢出是$2^{16}=65536$



#### 最小正数（下溢出）

$$
0\quad00000\quad0000000001 = 2^{-14}\times(0+\dfrac{1}{1024}) \approx 0.000000059604645
$$

注意，当指数为为00000时，计算公式发生变化了

$$
y_c = 2^{-14}\times(0+\dfrac{x_c}{1024})\\
x_c=0.5\\
y_c=2^{-25}
$$

这里临界值就是x取0.5，而且不是四舍五入，而是当x=0.5时，就直接舍去，整个值为0

主要是在
$$
\dfrac{x-N}{\sqrt{\sigma^2}}
$$
计算各种Norm时，防止分母下溢出导致的上溢出。



#### Nan

求softmax函数

无穷大除以无穷大，发生了混乱

迭代法求均值
$$
\begin{aligned}
\bar{x}_t & =\frac{1}{t} \sum_{i=1}^t x_{\mathrm{i}} \\
& =\frac{1}{t} \sum_{i=1}^{t-1} x_{\mathrm{i}}+\frac{1}{t} x_{\mathrm{t}} \\
& =\frac{t-1}{t}\bar{x}_{t-1}+\frac{1}{t} x_{\mathrm{t}} .
\end{aligned}
$$



### fraction

切1024个小区间


$$
(-1)^{\text {sign }} \times 2^{\text {exponent }-15} \times\left(1+\frac{\text { fraction }(10 \text { 进制 })}{1024}\right)
$$

最小正数：
$$
0\quad00001\quad0000000000 = 2^{-14}\times(1+\dfrac{0}{1024})\approx 0.00006103515625
$$


随大区间的数值的增大，区间宽度变大，但小区间都只拆分成1024份。数值越大，精度越低
$$
\begin{array}{ll}
2^{\wedge}(-2) \sim 2^{\wedge}(-1) & \text { 区间长度 } 2^{\wedge}(-2) \\
2^{\wedge}(-1) \sim 2^{\wedge} 0 & \text { 区间长度 } 2^{\wedge}(-1) \\
2^{\wedge} 0 \sim 2^{\wedge} 1 & \text { 区间长度 } 2^{\wedge} 0 \\
2^{\wedge} 1 \sim 2^{\wedge} 2 & \text { 区间长度 } 2^{\wedge} 1 \\
2^{\wedge} 2 \sim 2^{\wedge} 3 & \text { 区间长度 } 2^{\wedge} 2 \\
\cdots &
\end{array}
$$


## FP32

32位，占4个byte

sign (1bit)  exponent(8bit)  fraction(23bit)

把小区间切的更细



## BF16

16位，占2个字节

sign (1bit) exponent(8bit) fraction(7bit)

能表示的数值大小和FP32一样，但精度很差

大模型使用这个，只是为了表示更大的数。主要是为了防止上溢出。



## 显存占用

模型参数量为$\Phi$个参数

训练要求：模型参数(fp16)、模型梯度(fp16)、Adam状态(fp32模型参数备份、fp32momentum、fp32variance)

剩余状态：激活值(activation)、各种临时缓冲区(buffer)、无法使用的显存碎片(fragmentation)

1个fp16占两个Byte。

#### 训练占用显存：$2\Phi + 2\Phi + 3\times4\Phi=16\Phi$

#### 推理占用显存$2\Phi$



## 计算精度

下溢出：数值太小，精度不够，训练到后期loss不变化

​	人为给Loss放大，解决下溢出

上溢出：exp相关的运算

​	人为给$m_j-max(m_i)$

舍入误差：一个大数+一个小数，加了等于没加

​	先转成32，再相加
