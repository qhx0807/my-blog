---
title: 将金额转换为大写人民币形式
date: 2017-05-22 23:28:25
tags: javascript
categories: 前端
---

最近在项目中处理收费划价的事情，遇到要把数字金额装换为大写的金额。如￥150.00 => 壹佰伍拾元整。把这个方法记录下来，说不定以后还会用到。

##### 需要用到的常量

```javascript
const digits = ["零", "壹", "贰", "叁", "肆", "伍", "陆", "柒", "捌", "玖"];
const radices = ["","拾", "佰", "仟"];
const bigRadices = ["", "万", "亿"];
const decimals = ["角","分"];
const CNY = "元"; 
const CN_INTEGER = "整";
const CN_RMB = "人民币";
const MAXNUM = 999999999999.99;
```

##### 对数字进行处理验证

```javascript
if (currencyStr == "")  return "无值！"; 
if (currencyStr.match(/[^,.\d]/) != null)  return "含有无效字符！"; 
if ((currencyStr).match(/^((\d{1,3}(,\d{3})*(.((\d{3},)*\d{1,3}))?)|(\d+(.\d+)?))$/) == null) return "格式不正确！"; 

currencyStr = currencyStr.replace(/,/g, "");
currencyStr = currencyStr.replace(/^0+/, "");
if (Number(currencyStr) > MAXNUM) return "金额过大，应小于10000亿元！"; 
```
* 将金额字符串分为整数部分和小数部分
```javascript
parts = currencyStr.split("."); 
if (parts.length > 1) { 
    integral = parts[0]; 
    decimal = parts[1].substr(0, 2); 
}
else {
    integral = parts[0]; 
    decimal = "";
}
```

##### 处理整数部分

```javascript
if(Number(integral) > 0){
zeroCount = 0;
for(let i = 0; i < integral.length; i++){
    p = integral.length - i - 1; 
    d = integral.substr(i, 1); 
    quotient = p / 4; 
    modulus = p % 4;
    if (d == "0") { 
        zeroCount++; 
    }else { 
        if (zeroCount > 0){ 
            outputStr += digits[0]; 
        } 
        zeroCount = 0; 
        outputStr += digits[Number(d)] + radices[modulus]; 
    }
    if (modulus == 0 && zeroCount < 4) { 
        outputStr += bigRadices[quotient]; 
        zeroCount = 0; 
    }
}
outputStr += CNY
}
```
<!-- more -->
##### 处理小数部分

```javascript
if (decimal != "") { 
    for (let i = 0; i < decimal.length; i++) { 
        d = decimal.substr(i, 1); 
        if (d != "0") { 
            outputStr += digits[Number(d)] + decimals[i]; 
        } 
    } 
}
```


##### 输出大写金额的字符串

```javascript
if (outputStr == "") { 
    outputStr = CN_ZERO + CN_DOLLAR; 
} 
if (decimal == "" || decimal == "0" || decimal == "00") { 
    outputStr += CN_INTEGER; 
}

outputStr = CN_RMB + outputStr; 

return outputStr;
```

##### 完整的代码

```javascript
const convertCurrency = (currency) => {
    const digits = ["零", "壹", "贰", "叁", "肆", "伍", "陆", "柒", "捌", "玖"];
    const radices = ["","拾", "佰", "仟"];
    const bigRadices = ["", "万", "亿"];
    const decimals = ["角","分"];
    const CNY = "元"; 
    const CN_INTEGER = "整";
    const CN_RMB = "人民币";
    const MAXNUM = 999999999999.99;
    let parts, integral, decimal, zeroCount, p, d, quotient, modulus;
    let outputStr = "";
    currencyStr = currency.toString();

    if (currencyStr == "")  return "无值！"; 
    if (currencyStr.match(/[^,.\d]/) != null)  return "含有无效字符！"; 
    if ((currencyStr).match(/^((\d{1,3}(,\d{3})*(.((\d{3},)*\d{1,3}))?)|(\d+(.\d+)?))$/) == null) return "格式不正确！"; 

    currencyStr = currencyStr.replace(/,/g, "");
    currencyStr = currencyStr.replace(/^0+/, "");
    if (Number(currencyStr) > MAXNUM) return "金额过大，应小于10000亿元！"; 

    parts = currencyStr.split("."); 
    if (parts.length > 1) { 
        integral = parts[0]; 
        decimal = parts[1].substr(0, 2); 
    }
    else {
        integral = parts[0]; 
        decimal = "";
    }
    //计算整数部分
    if(Number(integral) > 0){
        zeroCount = 0;
        for(let i = 0; i < integral.length; i++){
            p = integral.length - i - 1; 
            d = integral.substr(i, 1); 
            quotient = p / 4; 
            modulus = p % 4;
            if (d == "0") { 
                zeroCount++; 
            }else { 
                if (zeroCount > 0){ 
                    outputStr += digits[0]; 
                } 
                zeroCount = 0; 
                outputStr += digits[Number(d)] + radices[modulus]; 
            }
            if (modulus == 0 && zeroCount < 4) { 
                outputStr += bigRadices[quotient]; 
                zeroCount = 0; 
            }
        }
        outputStr += CNY
    }
    //计算小数部分
    if (decimal != "") { 
        for (let i = 0; i < decimal.length; i++) { 
            d = decimal.substr(i, 1); 
            if (d != "0") { 
                outputStr += digits[Number(d)] + decimals[i]; 
            } 
        } 
    }
    //输出
    if (outputStr == "") { 
        outputStr = CN_ZERO + CN_DOLLAR; 
    } 
    if (decimal == "" || decimal == "0" || decimal == "00") { 
        outputStr += CN_INTEGER; 
    }

    outputStr = CN_RMB + outputStr; 

    return outputStr;
}
```
