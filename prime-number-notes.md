// concurrent prime- number

public class ConcurrentPrimeFinder extends AbstractPrimeFinder {
- private final int poolSize;
- private final int numberOfParts;
-
5 public ConcurrentPrimeFinder(final int thePoolSize,
- final int theNumberOfParts) {
- poolSize = thePoolSize;
- numberOfParts = theNumberOfParts;
- }
10 public int countPrimes(final int number) {
- int count = 0;
- try {
- final List<Callable<Integer>> partitions =
- new ArrayList<Callable<Integer>>();
15 final int chunksPerPartition = number / numberOfParts;
- for(int i = 0; i < numberOfParts; i++) {
- final int lower = (i * chunksPerPartition) + 1;
- final int upper =
- (i == numberOfParts - 1) ? number
20 : lower + chunksPerPartition - 1;
- partitions.add(new Callable<Integer>() {
- public Integer call() {
- return countPrimesInRange(lower, upper);
- }
25 });
- }
- final ExecutorService executorPool =
- Executors.newFixedThreadPool(poolSize);
- final List<Future<Integer>> resultFromParts =
30 executorPool.invokeAll(partitions, 10000, TimeUnit.SECONDS);
- executorPool.shutdown();
- for(final Future<Integer> result : resultFromParts)
- count += result.get();
- } catch(Exception ex) { throw new RuntimeException(ex); }
35 return count;
- }


public static void main(final String[] args) {
- if (args.length < 3)
- System.out.println("Usage: number poolsize numberOfParts");
40 else
- new ConcurrentPrimeFinder(
- Integer.parseInt(args[1]), Integer.parseInt(args[2]))
- .timeAndCompute(Integer.parseInt(args[0]));
- }
45 }

Number of primes under 10000000 is 664579
Time (seconds) taken is 4.236507
