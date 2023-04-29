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

template IsEqual(){
    signal input in[2];
    signal output out;

    component isZero = IsZero();
    isZero.in <== in[0] - in[1] ;
    out <== isZero.out;
}

component main = IsEqual();

/* INPUT = {
    "in": ["231", "232"]
} */
```


## 选择器 Selector

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

template IsEqual(){
    signal input in[2];
    signal output out;

    component isZero = IsZero();
    isZero.in <== in[0] - in[1] ;
    out <== isZero.out;
}

template SumArray(length){
    signal input in[length];
    signal output out;

    signal sum[length];
    sum[0] <== in[0];
    for(var i = 1; i < length; i++){
        sum[i] <== sum[i-1] + in[i];
    }

    out <== sum[length-1];
}

template Selector(nChoices){
    signal input in[nChoices];
    signal input index;
    signal output out;


    component sum = SumArray(nChoices);
    component isEqual[nChoices];
    for(var i = 0; i < nChoices; i++){
        
        isEqual[i] = IsEqual();
        isEqual[i].in[0] <== index;
        isEqual[i].in[1] <== i;
        sum.in[i] <== in[i] * isEqual[i].out;
    }

    out <== sum.out;
}
component main = Selector(4);

/* INPUT = {
    "in": ["111", "222", "333", "444"],
    "index": "5"
} */
```
## 判负 IsNegative

## 少于 LessThan

## 整数除法 IntegerDivide
