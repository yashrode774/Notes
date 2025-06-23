## Internal working of ArrayList
- ArrayList uses array in the background
- Initially an empty array is created(Before java 8, on initialization the size of the array used to be 10) and when we add the first element the array size changes to 10
- When we exceed the current size, then the size is increased to `1.5x + 1`
