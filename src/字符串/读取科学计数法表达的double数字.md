# 读取科学计数法表达的double数字

## [Java 进阶](https://oi-wiki.org/lang/java-pro/)

> - ***Question***
>   - 读取科学计数法表达的double数字。

---

## *Java*

> - ***内置类***
>   - 用 `StreamTokenizer` 读取会出错。
>   - 用 `StringTokenizer` 读取正确但比较费内存。

```java
import java.io.*;
import java.util.StringTokenizer;

public class Main {

    /*
        输入如下
        5
        10.203E+0000
        5.6920E+0001
        30.888E-0001
        400.20E+0002
        0.2373E-0002
  */
    public static void main(String[] args) throws IOException {
        // test1();
        test2();
    }

    // 关于StringTokenizer
    // 以下的写法是oi-wiki推荐的
    public static class Kattio extends PrintWriter {

        private BufferedReader r;
        private StringTokenizer st;

        public Kattio() {
            this(System.in, System.out);
        }

        public Kattio(InputStream i, OutputStream o) {
            super(o);
            r = new BufferedReader(new InputStreamReader(i));
        }

        public Kattio(String intput, String output) throws IOException {
            super(output);
            r = new BufferedReader(new FileReader(intput));
        }

        public String next() {
            try {
                while (st == null || !st.hasMoreTokens())
                    st = new StringTokenizer(r.readLine());
                return st.nextToken();
            } catch (Exception _) {
            }
            return null;
        }

        public int nextInt() {
            return Integer.parseInt(next());
        }

        public double nextDouble() {
            return Double.parseDouble(next());
        }

        public long nextLong() {
            return Long.parseLong(next());
        }

    }

    // StreamTokenizer无法正确读取
    public static void test1() throws IOException {
        System.out.println("测试StreamTokenizer");
        System.out.println("输入 : ");
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StreamTokenizer in = new StreamTokenizer(br);
        PrintWriter out = new PrintWriter(new OutputStreamWriter(System.out));
        in.nextToken();
        int n = (int) in.nval;
        double num;
        for (int i = 1; i <= n; i++) {
            in.nextToken();
            num = (double) in.nval;
            out.println(num);
        }
        out.flush();
        out.close();
        br.close();
    }

    // StringTokenizer可以正确读取
    public static void test2() throws IOException {
        System.out.println("测试StringTokenizer");
        System.out.println("输入 : ");
        Kattio io = new Kattio();
        int n = io.nextInt();
        double num;
        for (int i = 1; i <= n; i++) {
            num = io.nextDouble();
            io.println(num);
        }
        io.flush();
        io.close();
    }

}
```
