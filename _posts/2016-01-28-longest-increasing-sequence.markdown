---
layout: post
title:  "Finding the Longest Increasing Sequence"
date:   2016-01-28 15:21:40 +0100
categories: dynamic programming
---

I just ran into a problem on the excellent [Hackerrank website][hackerrank].

The problem is:

Given an array of integers, find the length of the longest strictly increasing sequence.

Where we define the strictly increasing sequence as a non-contigous subarray
of the given array where the order of the values is kept and the values
are strictly increasing.

An example is way better to understand:

If the given array is `2 7 4 3 8`, the longest increasing sequence are `2 4 8` or `2 7 8`
or `2 3 8`. So the output is 3.

The naive solution
==================

The naive solution is to check all the possible sequences and find the longest one:

~~~
int LIS(int a[], int n) {
    std::vector<int> L[n];
    L[0].push_back(a[0]);

    for (int i = 1; i < n; ++i) {
        for (int j = 0; j < i; ++j) {
            // If we find bigger than v, we take it and "add" a[j].
            if (a[j] < a[i] && L[i].size() < L[j].size() + 1) {
                L[i] = L[j];
            }
        }

        L[i].push_back(a[i]);
    }

    return L[n-1].size();
}
~~~

This solution will work but has a $O(N^2)$ complexity, which is not acceptable.

The fast solution
=================

I tried some ideas here and there, but I had the intuition this could be solvable
with a Binary Indexed Tree (BIT) because when you have that kind of double loop, it is
often solvable with that data structure.

If you want to understand what a BIT is, you can look at [this post on the geeksforgeeks website][bit_explain].
It is kind of hard to understand the principle on first sight, but it is in fact quite easy once you get
the good hints (maybe I will write a post about it later).

The solution I came with is a $O(nlog(n))$ with an extra $O(n)$ space. The first thing you need to
do is to sort the array and keep track of the indices (1-based indices). This way, you can resolve the exact same
problem with the indices. But this time, you are assured that the values are uniques, its fills the interval $[1; n]$,
and you get the values in increasing order.

To better visualize the algorithm, I will put an image of a BIT of size 7.

<center>
    <img src="https://docs.google.com/drawings/d/1ZjNEilhJVogTSllTy5JBplXMqhykB0vL_na5NUHLfis/pub?w=348&h=199" />
</center>

What you want to store in your BIT is the length of the longest increasing sequence you have seen
so far.

Storing the lengths is simple, you go up in your tree, always taking the right parent (for example
if you are on the node labeled 3, you will go on the node labeled 4) and update
the BIT between the max of the current LIS you found and the others you stored.

~~~
// Args:
//  bit: the BIT array, it is a compact version of the tree.
//  i: the indice of the current value.
//  n: the size of the BIT.
int bit_update(int bit[], int i, int n) {
    // We get the longest increasing sequence so far.
    int max_prev_lis = bit_get_max(bit, i, n);
    int max = max_prev_lis + 1;

    while (i <= n) {
        // We want to store the max when we go up in the tree.
        max = std::max(max, bit[i]);

        bit[i] = max;
        i += (i & -i);
    }

    return max;
}
~~~

Now that we defined the way of storing the LIS, we need to code the function that will
get the previous LIS seen so far. Again, the code is simple:

~~~
// Args:
//  bit: the BIT array, it is a compact version of the tree.
//  i: the indice of the current value.
//  n: the size of the BIT.
int bit_get_max(int bit[], int i, int n) {
    int max = 0;

    while (i > 0) {
        max = std::max(max, bit[i]);
        i -= (i & -i);
    }

    return max;
}
~~~

As you can see, the code again is very short. The principle is the same as before but this time
we go up in the tree going on the left parent (if you are the node labeled 3 you will go to the
node 2). This way, you will encounter only the numbers that are shorter than the current one
(because you sorted your array) and you will get the LIS that is under your current indice.

The code only works because we are working with the indices of the sorted values. A special mention
about that is you must sort prior to the values in ascending order, but on the indices
in descending order if the values are equal.

Let's say you have an input from the standard input of the form:

~~~
5
1 2 3 4 5
~~~

Here is the full solution that output to stdout the length of the longest increasing
sequence:

~~~
int bit_get_max(int bit[], int i, int n) {
    int max = 0;

    while (i > 0) {
        max = std::max(max, bit[i]);
        i -= (i & -i);
    }

    return max;
}

int bit_update(int bit[], int i, int n) {
    // We get the longest increasing sequence so far.
    int max_prev_lis = bit_get_max(bit, i, n);
    int max = max_prev_lis + 1;

    while (i <= n) {
        // We want to store the max when we go up in the tree.
        max = std::max(max, bit[i]);

        bit[i] = max;
        i += (i & -i);
    }

    return max;
}

struct Value {
    int val;
    int i;
};

int main(void) {
    int n;
    std::cin >> n;

    Value arr[n];
    for (int i = 0; i < n; ++i) {
        int v;
        std::cin >> v;

        arr[i] = {v, i + 1};
    }

    std::sort(arr, arr+n, [](Value const& a, Value const& b) {
        if (a.val == b.val)
            return b.i < a.i;
        return a.val < b.val;
    });

    int best = 1; // The shortest increasing sequence is always one.

    int bit[n+1];
    std::memset(bit, 0, sizeof (bit));

    for (int i = 0; i < n; ++i) {
        auto c = arr[i];

        best = std::max(best, bit_update(bit, c.i, n));
    }

    std::cout << best << std::endl;
}
~~~

I hope the explanation was clear,
happy coding !

[hackerrank]: https://www.hackerrank.com/challenges/longest-increasing-subsequent
[bit_explain]: http://www.geeksforgeeks.org/binary-indexed-tree-or-fenwick-tree-2/ "Binary Indexed Tree"
