# merge
3-Way Merge

#Problem One: Implement three-way merge sort in Python. It should 
#at a minimum accept lists of integers as input.

%matplotlib inline
from time import clock
import matplotlib.pyplot as plt
import math
import random

def mergelist_3(alist):
    #creates an empty array to store the sorted values in
    result = []
    #creates a loop which will break after list is empty 
    while alist:
        #setting min value as the first element in list
        min_value, index = alist[0][0],0
        #goes through each list
        for list in alist:
            #comparing first value of each list with current min & updating accordingly
            if list[0]<min_value:
                min_value = list[0]
                index = alist.index(list)
        #adds the minimun value to the result array
        result.append(min_value)
        #removing and returning the first item in the nested list corresponding to index
        alist[index].pop(0)
        #checking nested list and removing it if empty 
        if not alist[index]:
            alist.remove(alist[index])
    return result

def mergesort_3(list):
    #checking if list has only one element and if so returning it
    if len(list)==1:
        return list
    else:
        #creates an interval that separates an array into three subarrays
        interval = int(math.ceil(len(list)/3))
        #sorting recursively
        list = [mergesort_3(list[i:i+interval])for i in range(0,len(list),interval)]
        return mergelist_3(list)

#sort) Problem 2: Implement a second version of three-way merge sort that 
#calls insertion sort when sublists are below a certain length

#Here is insertion sort code that I prepared for a previous class.
#This code builds the final sorted array one item at a time, making 
#it very inefficient when dealing with arrays of large size.

def insertion_sort(alist):
    #goes through list
    for i in range(1, len(alist)):
        #setting value equal to current index 
        value = alist[i]
        #creating a loop so that the algorithm will terminate
        while value <= alist[i-1] and i > 0:
            #comparing value with the value of previous element
            alist [i] =  alist[i-1]
            i -= 1
        alist[i] = value
    return alist 

def aug_sort(alist):
    #loop that calls insertion sort as long as list size is less than 10
    #when input size is larger applies merge sort
    if len(alist)<10:
        return insertion_sort(alist)
    else: 
        interval = int(math.ceil(len(alist)/float(3)))
        alist = [mergesort_3(alist[i:i+interval])for i in range(0,len(alist),interval)]     
    return mergelist_3(alist) 

#Problem 4: (#complexity, #optimalalgorithm) Analyze and compare the practical run 
#times of regular merge sort, three-way merge sort, and the augmented 
#merge sort from (2). Make sure to define what each algorithm's complexity 
#is and to enumerate the explicit assumptions made to assess each algorithm's 
#run time. Your results should be presented in a table, along with an 
#explanatory paragraph and any useful graphs or other charts to document 
#your approach. Part of your analysis should indicate whether or not there is 
#a “best” variation. Compare your benchmarks with the theoretical result we have discussed in class.

#This is Merge Sort code I prepared for class 2.1. Here I will compare
#the running times of 3-way merge, augmented and merge sort.

def merge_list (A,B):
    i = 0
    j = 0
    step = 0
    #creates an array to place the sorted array
    result = []
    #merges the two subarrays 
    while i<len(A) and j<len(B):
        step +=1
        if A[i] <= B[j]:
            result.append(A[i])
            i += 1
            step +=1
        else:
            result.append(B[j])
            j +=1
            step +=1
    result += A[i:]
    step+=1
    result += B[j:]
    step +=1
    return result
    return step

def merge_sort(C):
    #checks to see if list is less than or equal to 1 and if so returns the list 
    if len(C) <= 1:
        return C
    else:
        #creates a midpoint to split the array in two 
        #creates two subarrays of equal size 
        midpoint = int(len(C)/2)
        A = merge_sort(C[:midpoint]) #right subarray
        B = merge_sort(C[midpoint:]) #left subarray
        return merge_list(A,B)

#test 
number_list = [100,11,1,41,53,75,22,83,13,32]
merge_sort(number_list) 

#The following code has been adapted from github in order to compare the running 
#times of merge, augmented merge and k-sort. 
#https://github.com/davejm/sorting-algorithms-comparison/blob/master/sorting.py

def test(algorithm_type, a):
#Creating a copy of the unsorted input a
    copy = a[:]
#Setting start time
    start = clock()
#Running the algorithm on the copy of the unsorted input a
    algorithm_type(copy)
#Calculating the time
    duration = clock() - start
    return duration
x = []
y_merge = []
y_merge3 = []
y_augmerge = []

for i in range(100, 1000000, 10000):
    a = random.sample(range(1000), 60)
    x.append(i)
    y_merge.append(test(merge_sort, a))
    y_merge3.append(test(mergesort_3, a))
    y_augmerge.append(test(aug_sort, a))
plt.plot(x, y_merge, color="r", label="Merge Sort")
plt.plot(x, y_merge3, color="b", label="3-Way Merge Sort")
plt.plot(x, y_augmerge, color="y", label="Augmented Merge Sort")
plt.xlabel("Numbers in the list")
plt.ylabel("Time taken (seconds)")
plt.title("Time to sort a list")
plt.legend(bbox_to_anchor=(1.1, 1), loc=2, borderaxespad=0.)
plt.show()
