# 第2课 课后作业

## 第1题 转换为bit位 Num2Bits
```
pragma circom 2.1.4;


template Num2Bits (nBits) {
    signal input in;
    signal output b[nBits];
    
    var sum = 0;
    for(var i = 0; i < nBits; i++){
        b[i] <-- (in \ (2** i)) % 2;
        sum = sum + b[i] * 2**i;
        0 === b[i] * (b[i] -1);
    }

    in === sum;

}

component main  = Num2Bits(3);

/* INPUT = {
    "in": "5"
} */
```

## 第2题 判零 IsZero
```
pragma circom 2.1.4;

template IsZero () {
    signal input in;
    signal output out;

    signal inv;

    inv <-- in == 0? 0 : 1/in;

    out <== -in * inv + 1;
    0 === in * out;    
}

component main = IsZero();

/* INPUT = {
    "in": "0"
} */
```

## 第3题 相等 IsEqual

## 选择器 Selector

## 判负 IsNegative

## 少于 LessThan

## 整数除法 IntegerDivide
