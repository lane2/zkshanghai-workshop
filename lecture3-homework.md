# 第3课 课后作业

给定整数x,m，如果x在模m下是二次剩余，即存在整数s，使得s2=x(mod m)，则记作 QR(m,x)=1; 如果x在模m下不是二次剩余，则记作QR(m,x)=0 。
## 第1题 二次非剩余 Quadratic nonresidue
假定：证明者可以为所有m,x计算QR(m,x)
公共输入：正整数m,x
目标：证明者想要说服验证者QR(m,x) = 0

协议
1. Verifier 在与m互质的元素中均匀地随机选取一个s，同时抛硬币b, b属于0或1。 设
 y= s*s*x if b=0, y=s*s if b =1
Verifier 将 y发送给 Prover 并挑战 Prover 以确定 b。

2. Prover 计算QR(m,y)并将其值发送回 Verifier

3. 如果 Verifier 从 Prover 收到的值确实等于b，则 Verifier 接受； 否则它拒绝。

完备性证明：

为了证明协议的完备性，我们需要证明如果 QR(m,x) = 1，那么 Prover 可以通过协议向 Verifier 证明 QR(m,y) = b 的正确性。

假设 QR(m,x) = 1，即 x 在模 m 下是二次剩余。因此，存在整数 s，使得 s^2 = x (mod m)。我们将 s 选择为 Verifier 在协议中挑战时选择的元素。因为 s 是模 m 下的二次剩余，所以 s^2 也是模 m 下的二次剩余。

根据协议，如果 Verifier 选择 b=1，那么 y = s^2，在模 m 下也是二次剩余。因此，QR(m,y) = 1，Prover 可以通过发送 QR(m,y) = 1 来向 Verifier 证明其正确性。

如果 Verifier 选择 b=0，那么 y = s^2 * x，在模 m 下也是二次剩余。因此，QR(m,y) = 1，Prover 可以通过发送 QR(m,y) = 1 来向 Verifier 证明其正确性。

因此，如果 QR(m,x) = 1，那么 Prover 可以通过协议向 Verifier 证明 QR(m,y) = b 的正确性，因此协议是完备的。


正确性证明：
这个协议的正确性可以通过以下方式证明：

假设 QR(m,x) = 0，即 x 在模 m 下不是二次剩余。我们需要证明的是，对于任何 Prover，Verifier 在执行上述协议时，以高概率拒绝 Prover 的欺骗行为。

首先，我们注意到，如果 Prover 发送的 QR(m,y) 的值与 Verifier 挑战时选择的 b 不同，那么 Verifier 将拒绝 Prover 的声明。这是因为 Prover 实际上声称 QR(m,y) = b，如果 QR(m,y) 的值与 b 不同，那么 Prover 明显是在欺骗。

现在考虑 Prover 发送的 QR(m,y) 的值与 Verifier 挑战时选择的 b 相同的情况。根据协议，如果 QR(m,y) = b，那么 Verifier 将接受 Prover 的声明。因此，我们需要证明的是，如果 QR(m,x) = 0，那么 QR(m,y) = b 的概率很小。

如果 QR(m,x) = 0，那么 x 在模 m 下不是二次剩余。因此，如果 Verifier 选择的 s 在模 m 下也不是二次剩余，那么 y = s^2 * x 在模 m 下也不是二次剩余。在这种情况下，QR(m,y) = b 的概率为 1/2，即与随机选择的 b 相同的概率。

另一方面，如果 Verifier 选择的 s 在模 m 下是二次剩余，那么 y = s^2 * x 在模 m 下有一半的概率是二次剩余，即 QR(m,y) = 1 的概率为 1/2。因此，如果 Verifier 选择的 s 在模 m 下是二次剩余，那么 QR(m,y) = b 的概率为 1/2，即与随机选择的 b 相同的概率。

由于 Verifier 在与 m 互质的元素中均匀地随机选择 s，因此选择 s 在模 m 下是二次剩余和不是二次剩余的概率相等。因此，如果 QR(m,x) = 0，那么 QR(m,y) = b 的概率为 1/2 * 1/2 + 1/2 * 1/2 = 1/2，即与随机选择的 b 相同的概率。

因此，如果 QR(m,x) = 0，那么 Prover 欺骗 Verifier 的概率为 1/2，即协议的正确性得证。






## 第2题 二次剩余 Quadratic residue


## 第3题 双线性自映射意味着DDH的失效 Self-pairing implies failure of DDH

设G 和GT 是相同素数阶q的阿贝尔群（以加法写出）。设g属于G 为生成元。假设我们有
一个可有效计算的非退化双线性对
e:G× G->GT
给出一个有效的決策算法，给定 ag,bg,y属于G(其中 a,b属于Zq为末知），判断是否a*Bg=y

提示：计算与g的映射。
此练习表明确定性 Diftie-He llman （DDH)假设对于双线性自映射是错误的。


解答：
给定 ag,bg,y 属于 G，我们可以使用以下步骤来判断是否 a*Bg=y：

选择随机数 r 属于 Zq。
计算 A = g^a 和 B = g^b。
计算 C1 = e(A, B)^r 和 C2 = e(g, y)^r。
如果 C1 = e(g, y)^r，则输出 "aBg=y"，否则输出 "aBg≠y"。
这个算法的正确性可以通过以下方式证明：

如果 aBg=y，则存在整数 k，使得 aBg = g^k * y。我们可以将 k 表示为 k = log_g (a*Bg/y)，然后将其代入到 C1 和 C2 的计算中。我们得到：

C1 = e(A, B)^r = e(g^a, g^b)^r = e(g, g)^(abr) = 1^(abr) = 1

C2 = e(g, y)^r = e(g, a*Bg/y)^r = e(g^a, g^b)^r * e(g, y/y)^r = e(A, B)^r * 1^r = C1 * 1 = C1

因此，如果 aBg=y，则 C1 = C2，算法输出 "aBg=y"。

## 第4题 BLS 签名聚合 BLS signature aggregation
