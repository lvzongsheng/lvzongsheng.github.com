---
layout: post
title: "Longest Palindromic Substring"
date: 2013-12-05 19:11
comments: true
categories: 
---
最长回文子串是一个比较基础的问题，在面试中也会经常被问题，所以自己也尝试总结一下解决回文子串的方法。可以参见[LeetCode题](http://oj.leetcode.com/problems/longest-palindromic-substring/)
##方法1：暴力法
采用暴力法是能够解决最长回文子串问题的，但是所花费的时间是非常巨大的，一般都不能达到要求。<br/>

    class Solution {
    public:
    string longestPalindrome(string s) {
        // IMPORTANT: Please reset any member data you declared, as
        // the same Solution instance will be reused for each test case.
        int index1 = 0, index2 = 0;
        int begin = 0, end = 0;
        int max = 0;
        for(int i=0; i<s.length();i++){
        	for(int j=i;j<s.length();j++){
        		index1 = i;
        		index2 = j;
        		while(index1<=index2){
        			if(s.at(index1)!=s.at(index2)){
        				break;
        			}else{
        				index1++;
        				index2--;
        			}
        		}
        		if(index1>index2 && j-i+1>max){
        			begin = i;
        			end = j;
        			max = j-i+1;
        		}
        	}
        }
        return s.substr(begin,end-begin+1);
    }
    };
从上面的代码中可以知道，暴力解法的时间复杂度为O(N^3),但我们也可以对暴力发进行一定的优化。我们从字符串的最长值向0进行循环，这样可以尽快的找到最长子串。这里就不写代码了。
<!--More-->
##动态规划法
我们可以采用动态规划的方法来求解。暴力解法之所以时间复杂度高，因为在求解过程中有很多的重复计算，打个简单地比方说：如果P不是回文子串，则aPb这种形式的也不是回文子串，以此类推则可以找到动态规划的算法。<br/>
动态规划的方法如下：
    P[i][j] = true 表示i~j这个子串是为回文子串，否则则不是。  
    那么我们可知
    P[i][j] = true if i>=j
    P[i][j] = true if S[i]==S[j] and P[i+1][j-1] = true
    else P[i][j] = false
使用动态规划的方法的时间复杂度是O(N^2)其动态规划的实现方法如下：<br/>
    class Solution {
    public:
    string longestPalindrome(string s) {
        // IMPORTANT: Please reset any member data you declared, as
        // the same Solution instance will be reused for each test case.
        bool isPalindrome[s.length()][s.length()];
        for(int i=0;i<s.length();i++){
            for(int j=0;j<s.length()-i;j++){
                if(j==j+i){
                    isPalindrome[j][j+i] = true;
                }else{
                    if(s[j]==s[j+i]){
                        if(i>1)
                            if(isPalindrome[j+1][j+i-1])
                                isPalindrome[j][j+i] = true;
                            else
                                isPalindrome[j][j+i] = false;
                        else
                            isPalindrome[j][j+i] = true;
                    }else{
                        isPalindrome[j][j+i] = false;
                    }
                }
            }
        }
        
        for(int i=s.length()-1; i>=0; i--){
            for(int j=0;j<s.length()-i;j++){
                if(isPalindrome[j][j+i])
                    return s.substr(j,i+1);
            }
            
        }
    }
    };
##Manacher算法
虽然动态规划的方法性能得到了提高，但还是在平方级上的，而使用Manacher算法，时间复杂度为O(N)。大家可以[参考该博文](http://www.cnblogs.com/biyeymyhjob/archive/2012/10/04/2711527.html)