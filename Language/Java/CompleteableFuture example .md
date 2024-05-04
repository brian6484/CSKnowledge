## Example 
Let's say we are reading an excel file of 10k rows or more and trying to check which rows has duplicate values in column
1 or 2

```java
public void checkForDuplicates(String filePath, int columnToCheck, int numThreads) throws IOException {
    ##hashmap will containe the value of excel file column as KEY and row index as Value
    final Map<String, List<Integer>> valueToRowMap = new ConcurrentHashMap<>();

    ##set will contain the duplicate excel values
    final Set<String> duplicates = Collections.syncrhonizedSet(new HashSet<>());

    try (FileInputStream fis = new FileInputStream(filePath);
         Workbook workbook = new XSSFWorkbook(fis)) {
        Sheet sheet = workbook.getSheetAt(0);
        List<Row> rows = new ArrayList<>();
        sheet.forEach(rows::add);

        ##divide the total rows to how many segments for each thread to process
        int chunkSize = (int) Math.ceil((double) rows.size() / numThreads);

        ##divide the total rows to how many rows for elements for each thread to process
        List<List<Row>> chunks = IntStream.range(0, numThreads)
            .mapToObj(i -> rows.subList(i * chunkSize, Math.min((i + 1) * chunkSize, rows.size())))
            .collect(Collectors.toList());

        ExecutorService executor = Executors.newFixedThreadPool(numThreads);

        List<CompletableFuture<Void>> futures = chunks.stream()
            .map(chunk -> CompletableFuture.runAsync(() -> {
                for (Row row : chunk) {
                    Cell cell = row.getCell(columnToCheck); // Use columnToCheck to get the cell
                    if (cell != null) {
                        String value = cell.toString().trim(); // Extract the cell value
                        int rowIndex = row.getRowNum() + 1; // Get the 1-based row number
                        synchronized (valueToRowMap) {
                            if (valueToRowMap.containsKey(value)) {
                                duplicates.add(value);
                                valueToRowMap.get(value).add(rowIndex);
                            } else {
                                valueToRowMap.put(value, Collections.singletonList(rowIndex));
                            }
                        }
                    }
                }
            }, executor))
            .collect(Collectors.toList());

        CompletableFuture.allOf(futures.toArray(new CompletableFuture[0])).join();
        executor.shutdown();
        
        System.out.println("Duplicates found:");
        duplicates.forEach(value -> {
            List<Integer> rowsWithDuplicates = valueToRowMap.get(value);
            System.out.println("Value: " + value + ", Rows: " + rowsWithDuplicates);
        });
    }
}
```

## Waiting for all asyn tasks to complete
```java
CompletableFuture.allOf(futures.toArray(new CompletableFuture[0])).join();
```

This is a good way to wait for asyn tasks to all complete, as well as creating an array of CompletableFuture instances with the correct
size. 
