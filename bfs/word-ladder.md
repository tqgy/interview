## Word Ladder


### 描述

Given two words (start and end), and a dictionary, find the length of shortest transformation sequence from start to end, such that:

* Only one letter can be changed at a time
* Each intermediate word must exist in the dictionary

For example, Given:

```
start = "hit"
end = "cog"
dict = ["hot","dot","dog","lot","log"]
```

As one shortest transformation is `"hit" -> "hot" -> "dot" -> "dog" -> "cog"`, return its length `5`.

Note:

* Return 0 if there is no such transformation sequence.
* All words have the same length.
* All words contain only lowercase alphabetic characters.


### 分析

求最短路径，用广搜。


### 单队列

{% if book.java %}
```java
// Word Ladder
// 时间复杂度O(n)，空间复杂度O(n)
public class Solution {
    public int ladderLength(String beginWord, String endWord, Set<String> wordList) {
        Queue<State> q = new LinkedList<>();
        HashSet<State> visited = new HashSet<>(); // 判重

        final Function<State, Boolean> stateIsValid = (State s) ->
                wordList.contains(s.word) || s.word.equals(endWord);
        final Function<State, Boolean> stateIsTarget = (State s) ->
                s.word.equals(endWord);

        final Function<State, HashSet<State> > stateExtend = (State s) -> {
            HashSet<State> result = new HashSet<>();

            char[] array = s.word.toCharArray();
            for (int i = 0; i < array.length; ++i) {
                final char old = array[i];
                for (char c = 'a'; c <= 'z'; c++) {
                    // 防止同字母替换
                    if (c == array[i]) continue;

                    array[i] = c;
                    State newState = new State(new String(array), s.level+1);

                    if (stateIsValid.apply(newState) &&
                            !visited.contains(newState)) {
                        result.add(newState);
                    }
                    array[i] = old; // 恢复该单词
                }
            }

            return result;
        };

        State startState = new State(beginWord, 0);
        q.offer(startState);
        visited.add(startState);
        while (!q.isEmpty()) {
            State state = q.poll();

            if (stateIsTarget.apply(state)) {
                return state.level + 1;
            }


            HashSet<State> newStates = stateExtend.apply(state);
            for (State newState : newStates) {
                q.offer(newState);
                visited.add(newState);
            }
        }
        return 0;
    }

    static class State {
        String word;
        int level;

        public State(String word, int level) {
            this.word = word;
            this.level = level;
        }

        @Override
        public int hashCode() {
            return word.hashCode();
        }

        @Override
        public boolean equals(Object other) {
            if (this == other) return true;
            if (this.hashCode() != other.hashCode()) return false;
            if (!(other instanceof State)) return false;

            return this.word.equals(((State) other).word);
        }
    }
}
```
{% endif %}


### 双队列

{% if book.java %}
```java
// Word Ladder
// 时间复杂度O(n)，空间复杂度O(n)
public class Solution {
    public int ladderLength(String beginWord, String endWord, Set<String> wordList) {
        Queue<String> current = new LinkedList<>(); // 当前层
        Queue<String> next = new LinkedList<>();    // 下一层
        HashSet<String> visited = new HashSet<>();  // 判重

        int level = -1;  // 层次

        final Function<String, Boolean> stateIsValid = (String s) ->
                wordList.contains(s) || s.equals(endWord);
        final Function<String, Boolean> stateIsTarget = (String s) ->
                s.equals(endWord);

        final Function<String, HashSet<String> > stateExtend = (String s) -> {
            HashSet<String> result = new HashSet<>();

            char[] array = s.toCharArray();
            for (int i = 0; i < array.length; ++i) {
                final char old = array[i];
                for (char c = 'a'; c <= 'z'; c++) {
                    // 防止同字母替换
                    if (c == array[i]) continue;

                    array[i] = c;
                    String newState = new String(array);

                    if (stateIsValid.apply(newState) &&
                            !visited.contains(newState)) {
                        result.add(newState);
                    }
                    array[i] = old; // 恢复该单词
                }
            }

            return result;
        };

        current.offer(beginWord);
        visited.add(beginWord);
        while (!current.isEmpty()) {
            ++level;
            while (!current.isEmpty()) {
                // 千万不能用 const auto&，pop() 会删除元素，
                // 引用就变成了悬空引用
                String state = current.poll();

                if (stateIsTarget.apply(state)) {
                    return level + 1;
                }

                HashSet<String> newStates = stateExtend.apply(state);
                for (String newState : newStates) {
                    next.offer(newState);
                    visited.add(newState);
                }
            }
            // swap
            Queue<String> tmp = current;
            current = next;
            next = tmp;
        }
        return 0;
    }
}
```
{% endif %}

### 相关题目

* [Word Ladder II](word-ladder-ii.md)
