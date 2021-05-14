# TakeHomePoints
Just for memo
1. To limit number of requests to third party which has API throttling (5 requests per second)
```
Guava RateLimiter vs Bucket4j in Java

Guava => Thread unsafe

@Test
  public void guavaTest() {
    RateLimiter rateLimiter = RateLimiter.create(5);
    long startTime = ZonedDateTime.now().getSecond();
    IntStream.rangeClosed(1, 20)
        .parallel()
        .forEach(i -> {
          //synchronized (obj) {
          boolean result = rateLimiter.tryAcquire(1, 200, TimeUnit.MILLISECONDS);
          if (result) {
            System.out.println("Get Acquired");
          } else {
            System.out.println("Get NON-Acquired");
          }
          //}
        });
    long elapsedTimeSeconds = ZonedDateTime.now().getSecond() - startTime;
    System.out.println("Elapsed Time: " + elapsedTimeSeconds);
  }
  
Bucket4j => Thread safe

@Test
  public void bucket4jTest() {
    Bandwidth limit = Bandwidth.classic(15, Refill.intervally(15, Duration.ofSeconds(1)));
    Bucket bucket = Bucket4j.builder()
        .addLimit(limit)
        .build();

    long startTime = ZonedDateTime.now().getSecond();
    IntStream.rangeClosed(1, 15).parallel().forEach(i -> {
      if (bucket.tryConsume(1)) {
        System.out.println("Consumed: " + i);
      } else {
        System.out.println("Not Consumed: " + i);
      }
    });
    long elapsedTimeSeconds = ZonedDateTime.now().getSecond() - startTime;
    System.out.println("\nElapsed Time: " + elapsedTimeSeconds);
  }
```
