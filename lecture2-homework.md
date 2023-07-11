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

LessThan能判断的值范围只有252位，用正常的负数（p=254位）会出错
```
pragma circom 2.1.4;

template Num2Bits(n) {
    signal input in;
    signal output out[n];
    var lc1=0;

    var e2=1;
    for (var i = 0; i<n; i++) {
        out[i] <-- (in >> i) & 1;
        out[i] * (out[i] -1 ) === 0;
        lc1 += out[i] * e2;
        e2 = e2+e2;
    }

    lc1 === in;
}


// Returns 1 if in (in binary) > ct
template CompConstant(ct) {
    signal input in[254];
    signal output out;

    signal parts[127];
    signal sout;

    var clsb;
    var cmsb;
    var slsb;
    var smsb;

    var sum=0;

    var b = (1 << 128) -1;
    var a = 1;
    var e = 1;
    var i;

    for (i=0;i<127; i++) {
        clsb = (ct >> (i*2)) & 1;
        cmsb = (ct >> (i*2+1)) & 1;
        slsb = in[i*2];
        smsb = in[i*2+1];

        if ((cmsb==0)&&(clsb==0)) {
            parts[i] <== -b*smsb*slsb + b*smsb + b*slsb;
        } else if ((cmsb==0)&&(clsb==1)) {
            parts[i] <== a*smsb*slsb - a*slsb + b*smsb - a*smsb + a;
        } else if ((cmsb==1)&&(clsb==0)) {
            parts[i] <== b*smsb*slsb - a*smsb + a;
        } else {
            parts[i] <== -a*smsb*slsb + a;
        }

        sum = sum + parts[i];

        b = b -e;
        a = a +e;
        e = e*2;
    }

    sout <== sum;

    component num2bits = Num2Bits(135);

    num2bits.in <== sout;

    out <== num2bits.out[127];
}


template IsNegative() {
    signal input in;
    signal output out;

    var p = 21888242871839275222246405745257275088548364400416034343698204186575808495617;
    // (p - 1) \ 2 = 10944121435919637611123202872628637544274182200208017171849102093287904247808
    component num2bits = Num2Bits(254);
    component compconstant = CompConstant((p - 1) \ 2);
    num2bits.in <== in;
    compconstant.in <== num2bits.out; 
    out <== compconstant.out;

}

component main = IsNegative(); 

/* INPUT = {
    "in": "21888242871839275222246405745257275088548364400416034343698204186575808495616"
} */

```

## 少于 LessThan

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

template LessThan(n) {
    assert(n <= 252);
    signal input in[2];
    signal output out;

    component n2b = Num2Bits(n+1);

    n2b.in <== (1<<n) + in[1] - in[0];

    out <== n2b.b[n];
}

component main = LessThan(4);

/*
INPUT = {
    "in": ["15", "14"]
}
*/
```
## 整数除法 IntegerDivide

```
pragma circom 2.1.4;

template Num2Bits(n) {
    signal input in;
    signal output out[n];

    for (var i = 0; i < n; i++) {
        out[i] <-- (in \ 2**i) % 2;
    }

    var accm = 0;
    for (var i = 0; i < n; i++) {
        accm += out[i] * 2**i;
    }
    accm === in;

    for (var i = 0; i < n; i++) {
        out[i] * (1 - out[i]) === 0;
    }
}

template LessThan (n) {
    assert(n <= 252);
    signal input in[2];
    signal output out;

    component n2b = Num2Bits(n+1);
    n2b.in <== in[0] - in[1] + (2**n); 
    out <== 1 - n2b.out[n];
}

template Range(nbits) {
    signal input in;
    signal bits[nbits];
    var bitsum = 0;
    for (var i = 0; i < nbits; i++) {
        bits[i] <-- (in >> i) & 1;
        bits[i] * (1 - bits[i]) === 0;
        bitsum = bitsum + 2 ** i * bits[i];
    }

    in === bitsum;
}

template IntegerDivide (nbits) {
    assert(nbits <= 126);
    signal input dividend;
    signal input divisor;

    signal output remainder;
    signal output quotient;
    
    component range1 = Range(nbits);
    range1.in <== dividend;
    component range2 = Range(nbits);
    range2.in <== divisor;

    signal inv;
    inv <-- 1 / divisor;
    inv * divisor === 1;

    quotient <-- dividend \ divisor;
    remainder <== dividend - quotient * divisor;

    component lessthan = LessThan(nbits);
    lessthan.in[0] <== remainder;
    lessthan.in[1] <== divisor;
    lessthan.out === 1;

    dividend === quotient * divisor + remainder;
}

component main = IntegerDivide(9);

/* INPUT = {
    "dividend": "135",
    "divisor": "35"
} */
```
