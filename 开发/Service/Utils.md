## Google Guava
- Range
```java
Range r1 = Range.closedOpen(3, 5); // [3, 5)
Range r2 = Range.openClosed(2, 7); // (2, 7]
Range r3 = Range.greaterThan(7); // (7, +∞)

/* Assert true */
// 包含
Preconditions.checkArgument(r2.contains(5));
Preconditions.checkArgument(r2.encloses(r1));
Preconditions.checkArgument(Range.encloseAll(Lists.newArrayList(1, 5, 9)).contains(3));
// the union of the ranges must form a connected set
Preconditions.checkArgument(r2.isConnected(r3));
// 交集
Preconditions.checkArgument(r2.intersection(r1).equals(r1));
// the minimal range that encloses both this range and other.
Preconditions.checkArgument(r1.span(r3).equals(Range.atLeast(3)));
```

## Apache Commons

