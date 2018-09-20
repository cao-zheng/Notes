# Integer(toHexString) 

> jdk 1.8

- toHexString(long):String
- toOctalString(long):String
- toBinaryString(long):String
```
//16进制
public static String toHexString(int i) {
     return toUnsignedString0(i, 4);
}
//8进制
public static String toOctalString(int i) {
    return toUnsignedString0(i, 3);
}
//2进制
public static String toBinaryString(int i) {
     return toUnsignedString0(i, 1);
}
```