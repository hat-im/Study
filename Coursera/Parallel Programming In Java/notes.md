# Parallel Programming In Java
Single-core computers execute programs sequentially. A sequence of steps is executed in order. 
This means that a given step will not be executed until all the steps preceding it have finished execution.

With multi-core computers, we can take advantage of their multitasking capabilities by deciding which steps in a program can be executed in parallel with each other, and how to coordinate the execution of these steps.

For example, consider the task of adding the elements of an array of numbers
```java
int array[] = {1, 2, 3, 4, 5, ...};
```
The sequential method of programming would iterate over all elements in order.
```java
int sum = 0;
for(int i = 0; i < array.length; ++i) {
	sum = sum + array[i];
}
```
In the parallel programming version, we split the array into two or more parts and calculate the sum of each part parallel. 
Then finally add the partial sums together at the end.
```java
int sum1 = 0, sum2 = 0;
finish {
	//first half calculated asynchronously
	async for(int i = 0; i < array.length / 2; ++i) { sum1 = sum1 + array[i]; }

	//second half calculated in parallel with first
	for(int i = array.length / 2; i < array.length; ++i) { sum2 = sum2 + array[i]; }
}
int sum = sum1 + sum2;
```
## Async-Finish Notations
We use the `async` notation to mark a particular task for being executed asynchronously (in a separate thread). 
The `finish` notation causes execution to half until all previously running tasks (asynchronous or not) are completed.
```java
finish {
	async S1;
	async S2;
	S3;
} //S1 and S2 are executed in parallel with S3.
```
## Fork-Join Framework
The fork-join framework is a Java implementation of async-finish functionalities.

To invoke `fork` on a task is to arrange for it to be asynchronously executed by executing it in a separate thread.
The `fork` invocation serves the same function as the `async` notation described previously.

We invoke `join` to return the result when all the running tasks created by `fork` have been completed successfully.
The `join` invocation is equivalent to the `finish` notation.

Java implements the async-finish functionality by extending `ForkJoinTask`, or its more specialized subclasses such as `RecursiveAction` or `RecursiveTask`.
Tasks created are executed in a `ForkJoinPool`, which contains threads for each task and coordinates their execution.
The `invoke()` and `invokeAll()` methods combine the `fork` and `join` operations of the tasks, executing them in parallel and waiting for their completion.
```java
public static class ArraySum extends RecursiveAction {
	int array[];
	int start, end;
	int sum;

	public ArraySum(int array[], int start, int end, int sum) {
		this.array = array;
		this.start = start;
		this.end = end;
		this.sum = sum
	}

	@Override
	protected void compute() {
		if(start == end) {
			sum  = array[start];
}
```
