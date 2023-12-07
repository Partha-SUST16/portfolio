---
date: '2022-07-08T11:50:54.000Z'
title: apache/commons-collections
tagline: Fix flaky test failure in SynchronizedBagTest#testCollectionToArray2
preview: Fix flaky test failure in SynchronizedBagTest#testCollectionToArray2
image: >-
  https://images.unsplash.com/photo-1656427868828-79a829b92b2b?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1332&q=80
---
### What is the purpose of this PR

- This PR fixes the flaky test  `org.apache.commons.collections4.bag.SynchronizedBagTest.testCollectionToArray2`
- The mentioned test may fail or pass without changes made to the source code when it is run in different JVMs due to `HashMap`'s non-deterministic iteration order.

### Why the test fails
- As the `SynchronizedBagTest` uses `HashBag` as its `collection` which uses `HashMap` internally and as per `Java 8` documentation, the ordering of `HashMap` is not constant, so assigning the same hashmap as an array to different objects can change the ordering. So when comparing these two arrays with `assertEquals` the test may fail as their ordering can be different.

### Reproduce the test failure
- Run the test with 'NonDex' maven plugin. The command to recreate the flaky test failure is 
  ` mvn -pl . edu.illinois:nondex-maven-plugin:1.1.2:nondex -debug -Dtest=org.apache.commons.collections4.bag.SynchronizedBagTest#testCollectionToArray2`

- Fixing the flaky test now may prevent flaky test failures in future Java versions.

### Expected result
- The tests should run successfully when run with 'NonDex' maven with the same command for recreating the flaky test.

### Actual Result
- We get the failure:
    `[ERROR] testCollectionToArray2  Time elapsed: 0.002 s  <<< FAILURE!
junit.framework.AssertionFailedError: toArrays should be equal expected:<[10, 11, Thirteen, null, Nine, 16, 14, Eight, , Three, 15, 12, 6.0, Seven, 4, One, 2, 5.0]> but was:<[4, 12, , 10, 16, One, Thirteen, Nine, null, 2, 5.0, 6.0, 15, Seven, 14, Eight, Three, 11]>
`
### Fix
- Create a `HashSet` from both arrays named `a` and `array` and check if they both are equal in `testCollectionToArray2`

This is the pr link : *[Fix flaky test failure in SynchronizedBagTest#testCollectionToArray2](https://github.com/apache/commons-collections/pull/336)*
