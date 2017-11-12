---
layout: post
title: "Math and string微软面试题目总结"
description: "微软面试类题目总结"
categories: [Interview]
tags: [Interview, Microsoft]
redirect_from:
  - /2013/04/22/
---
191. Number of 1 Bits

An Integer in Java has 32 bits, e.g. 00101000011110010100001000011010.
To count the 1s in the Integer representation we put the input int
n in bit AND with 1 (that is represented as
00000000000000000000000000000001, and if this operation result is 1,
that means that the last bit of the input integer is 1. Thus we add it to the 1s count.
ones = ones + (n & 1);
Then we shift the input Integer by one on the right, to check for the
next bit.
n = n>>>1;
We need to use bit shifting unsigned operation >>> (while >> depends on sign extension)

We keep doing this until the input Integer is 0.
In Java we need to put attention on the fact that the maximum integer is 2147483647. Integer type in Java is signed and there is no unsigned int. So the input 2147483648 is represented in Java as -2147483648 (in java int type has a cyclic representation, that means Integer.MAX_VALUE+1==Integer.MIN_VALUE).
This force us to use

n!=0
in the while condition and we cannot use

n>0
because the input 2147483648 would correspond to -2147483648 in java and the code would not enter the while if the condition is n>0 for n=2147483648.

public static int hammingWeight(int n) {
    int ones = 0;
        while(n!=0) {
            ones = ones + (n & 1);
            n = n>>>1;
        }
        return ones;
}
387. First Unique Character in a String

class Solution {
    public int firstUniqChar(String s) {
        int[] freq = new int[26];
        for (int i = 0; i < s.length(); i++) {
            freq[s.charAt(i) - 'a']++;
        }

        for (int i = 0; i < s.length(); i++) {
            if (freq[s.charAt(i) - 'a'] == 1) {
                return i;
            }
        }
        return -1;
    }
}
567. Permutation in String

Solution: we gonna use map store the characters in s1, then sliding a window with size len1 in s2, then when all counts of character in len1 reduced to 0, this means true.

class Solution {
    public boolean checkInclusion(String s1, String s2) {
        int len1 = s1.length(), len2 = s2.length();
        int[] map = new int[26];
        //first loop put s1 character into map and same time check the start of s2
        if (len1 > len2) return false;
        for (int i = 0; i < len1; i++) {
            map[s1.charAt(i) - 'a']++;
            map[s2.charAt(i) - 'a']--;
        }

        if (allZero(map)) return true;

        //use a sliding window, which size is len1
        for (int i = len1; i < len2; i++) {
            map[s2.charAt(i) - 'a']--;
            map[s2.charAt(i - len1) - 'a']++;
            if (allZero(map)) return true;
        }

        return false;
    }

    private boolean allZero(int[] map) {
        for (int i = 0; i < map.length; i++) {
            if (map[i] != 0) return false;
        }
        return true;
    }
}
Time complexity: O(N)

165. Compare Version Numbers

class Solution {
    public int compareVersion(String version1, String version2) {
    String[] levels1 = version1.split("\\.");
    String[] levels2 = version2.split("\\.");

    int length = Math.max(levels1.length, levels2.length);
    for (int i=0; i<length; i++) {
        Integer v1 = i < levels1.length ? Integer.parseInt(levels1[i]) : 0;
        Integer v2 = i < levels2.length ? Integer.parseInt(levels2[i]) : 0;
        int compare = v1.compareTo(v2);
        if (compare != 0) {
            return compare;
        }
    }

    return 0;
}
}
91. Decode Ways

A message containing letters from A-Z is being encoded to numbers using the following mapping:

'A' -> 1
'B' -> 2
...
'Z' -> 26
Given an encoded message containing digits, determine the total number of ways to decode it.

Solution: this problem contains dynamic programming and String

91. Decode Ways

Solution: used a dp array of size n + 1 to save subproblem solutions. dp[0] means an empty string will have one way to decode, dp[1] means the way to decode a string of size 1. I then check one digit and two digit combination and save the results along the way. In the end, dp[n] will be the end result.

public class Solution {
    public int numDecodings(String s) {
        if(s == null || s.length() == 0) {
            return 0;
        }
        int n = s.length();
        int[] dp = new int[n+1];
        dp[0] = 1;
        dp[1] = s.charAt(0) != '0' ? 1 : 0;
        for(int i = 2; i <= n; i++) {
            int first = Integer.valueOf(s.substring(i-1, i));
            int second = Integer.valueOf(s.substring(i-2, i));
            if(first >= 1 && first <= 9) {
               dp[i] += dp[i-1];
            }
            if(second >= 10 && second <= 26) {
                dp[i] += dp[i-2];
            }
        }
        return dp[n];
    }
}
O(N)