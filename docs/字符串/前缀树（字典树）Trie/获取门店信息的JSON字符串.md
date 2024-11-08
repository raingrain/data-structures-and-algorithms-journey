# 获得门店信息的JSON字符串

## 北京北明数科信息技术有限公司

> - **Question**
>   - 给定门店信息数组，门店信息类如下所示，请你将信息数组中的信息转化成JSON字符串，如下面主方法中的测试用例所示。

## Java

> - **前缀树**

```java
class Solution {
    
    // 系统给你的原始类
    public static class AreaResource {
        
        public String area;
        public String separator;
        public long count;
        
        public AreaResource(String area, String separator, long count) {
            this.area = area;
            this.separator = separator;
            this.count = count;
        }
        
    }
    
    public static class TrieNode {
        
        // 地区名称：中国
        // 四川
        // 成都
        public String name;
        //  中国   key : 省名   value: 下级节点
        public HashMap<String, TrieNode> nextMap;
        // 门店数
        public long count;
        
        public TrieNode(String name, long count) {
            this.name = name;
            this.count = count;
            this.nextMap = new HashMap<>();
        }
        
        public String toJSON() {
            StringBuilder ans = new StringBuilder();
            if (!name.equals("")) {
                // :   ""
                // str =  "中国":
                // str = "成都":
                ans.append("\"").append(name).append("\"").append(":");
            }
            // 填冒号后面的内容
            if (nextMap.isEmpty()) {
                // 现在来到的是叶子节点
                ans.append(count);
            } else {
                // "中国":{ 四川如何如何,河南如何如何,江苏如何如何}
                ans.append("{");
                // 括号中间的区域交给递归
                for (TrieNode next : nextMap.values()) {
                    ans.append(next.toJSON()).append(",");
                }
                // 把最后一个逗号变成括号
                ans.setCharAt(ans.length() - 1, '}');
            }
            return ans.toString();
        }
        
    }
    
    // 要实现的方法
    public static String mergeCount(List<AreaResource> areas) {
        TrieNode root = new TrieNode("", 0);
        for (AreaResource area : areas) {
            // 用分隔符获取地名列表
            String[] path = area.area.split(area.separator);
            createTrie(path, 0, root, area.count);
        }
        return root.toJSON();
    }
    
    // 递归建树
    private static void createTrie(String[] path, int index, TrieNode pre, long count) {
        if (index == path.length) {
            // 越界时上一个节点是叶子节点
            pre.count += count;
        } else {
            String cur = path[index];
            if (!pre.nextMap.containsKey(cur)) {
                pre.nextMap.put(cur, new TrieNode(cur, 0));
            }
            createTrie(path, index + 1, pre.nextMap.get(cur), count);
        }
    }
    
    public static void main(String[] args) {
        AreaResource a1 = new AreaResource("中国,四川,成都", ",", 10);
        AreaResource a2 = new AreaResource("中国,浙江,杭州", ",", 50);
        AreaResource a3 = new AreaResource("中国,浙江,杭州", ",", 25);
        AreaResource a4 = new AreaResource("中国,浙江,义乌", ",", 22);
        AreaResource a5 = new AreaResource("中国,四川,成都", ",", 15);
        AreaResource a6 = new AreaResource("中国,四川,攀枝花", ",", 12);
        AreaResource a7 = new AreaResource("中国,浙江,宁波", ",", 16);
        AreaResource a8 = new AreaResource("中国,海南,海口,琼山,府城,朱云路", ",", 16);
        AreaResource a9 = new AreaResource("中国,海南,海口,龙华", ",", 30);
        
        List<AreaResource> areas = new ArrayList<>();
        areas.add(a1);
        areas.add(a2);
        areas.add(a3);
        areas.add(a4);
        areas.add(a5);
        areas.add(a6);
        areas.add(a7);
        areas.add(a8);
        areas.add(a9);
        
        System.out.println(mergeCount(areas));
    }
    
}
```
