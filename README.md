# Picomatch
A tiny, sensible subset of regex.

* Zero dependencies (unless you count the C standard library)
* Completely cross-platform
* Tiny implementation
* No allocations or global state
* Concise, safe API

picomatch supports a wide subset of regex, including..
* Anchors `^ $ \b \B`
* Grouping and capturing `() (?:)`
* Sets, including ranges `[...] [^...] [a-zA-z]`
* Character classes `\s \S \w \W \d \D`
* Escaped characters `\n \r \t \0 \\`
* Quantifiers `+ +? * *? {1} {1,} {1,5} ?`

Unlike most other small regex libraries, picomatch's quantifiers support limited backtracking as well

## Usage
Here's a basic example using picomatch to identify email addresses
```c
// capture both the name and the provider
const char* regex = "^([\\w.\\-]{0,25})@(yahoo|hotmail|gmail)\\.com$";

// get the required allocation size for the compiled regex
// you can skip this step if you provide a sensible allocation, 
// and picomatch will gracefully error if it's not enough
const char* err = NULL;
size_t compiled_size = pm_expsize(regex, &err);  
if (compiled_size == 0) {  
    printf("Failed to size: %s\n", err);  
    return 1;  
}  

// Compile the regex!
pm_Regex* expr = malloc(compiled_size);  
if (!pm_compile(expr, compiled_size, regex)) {  
    printf("Failed to compile: %s\n", pm_geterror(expr));  
}  

// Get the number of capture groups required;
int num_groups = pm_getgroups(expr);
pm_Group* capture_groups = malloc(sizeof(pm_Group) * num_groups);  
memset(capture_groups, 0, sizeof(pm_Group) * num_groups);  
  
const char* source = "example-mail@yahoo.com";  
printf("\nMatches: %d\n", pm_match(expr, source, 0, capture_groups, num_groups, 0)); 
for (int i = 0; i < num_groups; i++) {  
    printf("\t%d - '%.*s'\n", i, capture_groups[i].length, source + capture_groups[i].start);  
}

// matches: 1
//     0 - 'example-mail@yahoo.com'
//     1 - 'example-mail'
//     2 - 'yahoo'
```

For more complex usage, refer to the documentation in `picomatch.h`

## License
picomatch is licensed under MIT, see the notice in the source files or `LICENSE` for details. 