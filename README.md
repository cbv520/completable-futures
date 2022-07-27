# completable-futures

```
public class TxRx {

    private final Map<String, CompletableFuture<Object>> futures = new HashMap<>();
    private final long timeoutMs = 30000;

    CompletableFuture<String> sendStringRequest(String id) {
        return send(id, "get-string", new CompletableFuture<>());
    }

    CompletableFuture<Integer> sendIntegerRequest(String id) {
        return send(id, "get-int", new CompletableFuture<>());
    }

    private <T> CompletableFuture<T> send(String id, String request, CompletableFuture<T> future) {
        future = future.completeOnTimeout(null, timeoutMs, TimeUnit.MILLISECONDS);
        future.thenAccept(e -> {
            if (e == null) {
                futures.remove(id);
                System.out.println(id + " expired");
            }
        });
        futures.put(id, (CompletableFuture<Object>) future);
        System.out.println("requesting: " + request);
        return future;
    }

    void receive(String id, Object value) {
        var future = futures.remove(id);
        if (future != null) {
            future.complete(value);
        } else {
            System.out.println("???");
        }
    }
}
```
