# gcd代码模板

## Java

> - ***模板***

```java
class Solution {

    public static int gcd(int a, int b) {
        return b == 0 ? a : gcd(b, a % b);
    }

}
```
