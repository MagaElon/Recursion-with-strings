# Recursion-with-strings
recursion with strings

def longest_palindromic_substring(s: str) -> str:
    """
    Find the longest palindromic substring in s.
    Uses recursive expand-around-center with memoization.
    Time: O(n²), Space: O(n²) with memo, O(1) extra space without counting memo
    """
    if not s:
        return ""
    
    memo = {}  # (left, right) -> length of palindrome centered there
    
    def expand_around_center(left: int, right: int) -> int:
        key = (left, right)
        if key in memo:
            return memo[key]
        
        # Base cases
        if left > right:
            return 0
        if left == right:
            memo[key] = 1
            return 1
        if left + 1 == right and s[left] == s[right]:
            memo[key] = 2
            return 2
            
        # Recursive case: expand if characters match
        if s[left] == s[right]:
            inner_len = expand_around_center(left + 1, right - 1)
            if inner_len == right - left - 1:  # valid palindrome
                memo[key] = inner_len + 2
                return memo[key]
        
        # Not a palindrome from this center
        memo[key] = 0
        return 0
    
    max_len = 0
    result = s[0]
    
    for i in range(len(s)):
        # Odd length palindromes (center at i)
        len1 = expand_around_center(i, i)
        # Even length palindromes (center between i and i+1)
        len2 = expand_around_center(i, i + 1)
        
        curr_max = max(len1, len2)
        if curr_max > max_len:
            max_len = curr_max
            start = i - (max_len - 1) // 2
            result = s[start:start + max_len]
    
    return result


# BONUS: Pure recursive version (no loop, true recursion fans only)
def longest_palindrome_recursive(s: str, memo=None) -> str:
    if memo is None:
        memo = {}
    
    def helper(i: int, j: int) -> tuple[int, int]:  # returns (length, start_index)
        if (i, j) in memo:
            return memo[(i, j)]
        if i > j:
            return 0, -1
        if i == j:
            return 1, i
            
        if s[i] == s[j]:
            length, start = helper(i + 1, j - 1)
            if length == j - i - 1:  # full palindrome
                memo[(i, j)] = (length + 2, i)
                return length + 2, i
        
        # Try skipping left or right
        len1, start1 = helper(i + 1, j)
        len2, start2 = helper(i, j - 1)
        
        if len1 >= len2:
            memo[(i, j)] = (len1, start1)
            return len1, start1
        else:
            memo[(i, j)] = (len2, start2)
            return len2, start2
    
    length, start = helper(0, len(s) - 1)
    return s[start:start + length]


# Test it
print(longest_palindromic_substring("babad"))        # "bab" or "aba"
print(longest_palindromic_substring("cbbd"))          # "bb"
print(longest_palindromic_substring("racecar"))       # "racecar"
print(longest_palindromic_substring("a"))             # "a"
