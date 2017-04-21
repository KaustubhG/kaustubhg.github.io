---
layout: post
title: "Bloom Filters Intro"
comments: true
description: "These give one of the two outputs: Either element may be present OR It definitely isn't"
keywords: "data structure, bloom filter"
author: Kaustubh
---


So I learnt about these in my Advanced Data Structures class. Pretty cool and powerful data structures these are!  
They are probablistic data structures which provide very fast lookups to test whether an element is a member of a set. The catch is that **_false positive matches_** are possible. This means that if the bloom filter says that element X is present in the set, then it 'may' be present. But if it says element X isn't present in the set, then it definitely is not. Due to this property they are mainly used to **filter out negative results**. 

![Bloom Filter](/assets/images/bloom_filter.jpg)

>So where do I use this data structure?  
One use-case: You can put it in your RAM and filter out negative result queries to avoid expensive disk I/Os!

Let me explain this:  
Suppose you have some huge data stored in your disk and its really expensive for you to perform a lookups. You don't want to be spending your time performing lookups that are going to fail cause now you spent all the time for nothing. In that case, you can place your bloom filter in your RAM and filter all elements which don't exist in the disk avoiding expensive disk IO. The only time your look up will now fail is the percent of false positive errors reported by the bloom filter. This number can be controlled (0.1% error is achievable) depending on various factors which I'll talk about in a later post.

Though this seems like state of the art stuff, this data structure is quite old: a guy named Burton Howard Bloom invented them in 1970! :)



#### Structure:
The bloom filter is basically just a bit vector and a few hashing functions. We use k hash functions on an element to add it to the bit vector. The real magic is in choosing the hash functions. They must be fast, independent and uniformly distributed. If you have k such functions then most of the work is already done in building a bloom filter.
Thus you see, you don't really **_store_** the 'actual' elements inside it. This makes it take up very less memory making it an attractive choice if you are prepared to tackle a few false positives!  

![Bloom Filter Structure](/assets/images/bloom_filter_structure.jpg)



#### Working:
The bloom filter contains 2 functions. 

``` c++
void insert(Element X);
bool contains(Element X);

```


Lets suppose we are adding an element X to the bloom filter (b_f).  
The k hash functions w'll use are named h_1(), h_2(), .. , h_k().
Inorder to add element X to the bloom filter, it is hashed k times and the output index bit is set given by each hash function is set in the bit vector.

i.e 
``` c++
void insert(BloomFilter b_f, Element X){
	b_f[h_1(X)] = 1;
	b_f[h_2(X)] = 1;
	.
	.
	.
	b_f[h_k(X)] = 1;
}
```


To test if an element is present in the set, we follow the same procedure of hashing the element k times. But this time we check whether all the output indexes set in the bit vector. If that is true, then the element may possibly be present.

``` c++
bool contains(BloomFilter b_f, Element X){

	if(b_f[h_1(X)] == 1 && b_f[h_2(X)] == 1 && .... && b_f[h_k(X)] == 1){
		return true //element may be present;
	}
	else return false //element is definitely not present;
}

```


To understand why the element may '**_possibly_**' be present, consider the following example.


![Bloom Filter Working](/assets/images/bloom_filter_working.jpg)


h1 and h2 are hashing functions 
The bloom filter has 2 elements 'hello' and 'hi' added.  
h_1('hello') = 5  
h_2('hello') = 7  
Thus b_f[5] = 1 and b_f[7] = 1  

h_1('hi') = 1  
h_2('hi') = 3  
Thus b_f[1] = 1 and b_f[3] = 1  

Now we want to check whether element 'hey' is present.  
Suppose that  
h_1('hey') = 1  
h_2('hey') = 5  

Now indexes 1 and 5 are already set in the bit vector. Although they were set while adding other elements, our bloom filter will report that element 'hey' is present even though we didn't add it! (That's a false positive)

But if 'hey' had hashed to values 0 and 4 respectively, both the indexes are 0 in the bit vector and thus we can surely say that 'hey' wasn't added to it.

Thus bloom filters allow false positives but not false negatives.


I'll write up another article about building a basic bloom filter using uniform hashing functions and maybe some math behind it.  
Thats all for now! :)








