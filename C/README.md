### Variables
Variables in C must be declared and initialized properly, reference sheet:

```C
// Native Types
char z = 'a';         // 1 Byte  - %c
int x = 0;            // 4 Bytes - %d
float y = 5.25        // 4 Bytes - %f
double z = 1.14159;   // 8 Bytes - %g
const int a = 1;      // Use the "const" keyword to make a variable read-only

// Variables outside functions are "global"
// - Available everywhere in the code
// - Allocated on the heap
int A = 1;
const double pi = 3.14;

// Variables inside functions are "local"
// - Available only inside the function
// - Allocated on the stack
void main(){
  int x = 1;
}

// Pointers are memory addresses of variables
int n = 5;    // We create an integer "n"
int* p = &n;  // We create a integer pointer "p", pointing to the "n" variable using &
*p = 1;       // The value contained in the pointer is accessed with *, changing this value will also change "n"
p++           // Pointers can be incremented to the next memory address, "p" does not point to "n" anymore
```

### Arrays
Arrays can be of two types, static or dynamic, they are declared and used in different ways:
```C
int main(){
  // Static arrays - Fixed or given length, iterable
  int array[10] = {0};                    

  int length = 5;                          
  int array[length];
  memset(array, 0, length*sizeof(int));

  for(int i = 0; i < length; i++){
    // Access "array[i]"
  }

  // Dynamic arrays - Variable length, non-iterable, must be freed after use
  int* array = malloc(sizeof(int)*length);
  memset(array, 0, length*sizeof(int));
  free(array);

  int* array = NULL;               // A different method, allows larger space
  array_space = mmap(NULL, length*sizeof(int), PROT_READ | PROT_WRITE, MAP_PRIVATE | MAP_ANONYMOUS, -1, 0);
  array = &array_space[starting_point]; 
  munmap(array_space, length);

  // Strings in C are dynamic arrays of chars, with two differences
  // 1 - They can be iterated, because they are terminated with '\0'
  char* str = "hello_world";
  for(char* q = str; *q != '\0'; q++){
    // Access the *q character
  }

  // 2 - They can be initialized in two different ways
  char* myString = "hello";               // Statically  - read-only, already terminated

  char* otherString = malloc(length+1);   // Dynamically - read/write, must be terminated
  otherString[length] = '\0';

  return 0;
}
```

### Structs

Structs are custom-made variable types, that can hold multiple variables inside. All the most important data structures used in algorithms will be some kind of `struct` object.
```C
// Defines a new type using a struct, called "customer_t", together with its attributes
typedef struct customer{
  char* name;
  int age;
} customer_t;

// Creates a "customer_t" variable, called "person", accessing the attributes with the dot (.)
customer_t person;
person.name = "Tony";
person.age = 35;

// Creates a "customer_t" pointer, accessing the attributes using the arrow (->)
customer_t* point;
point->name = "Tony";
point->age = 35;
```
### Functions

Functions are the building blocks of a code, they are usually declared before the `main()` function. They can be called in the `main()` to perform a variety of complicated operations. Some functions are already present in libraries

```C
// Includes libraries, containing functions such as "malloc()" or "printf()"
#include <stdio.h>
#include <stdlib.h>

// Creates a function that takes two integers and return an integer
int sum(int x, int y){
  return x + y;
}

// Creates a function that takes a string and its length, and returns its inverse
char* inverse(const char* str, int length ){
  char* ret = malloc(length+1);      // Allocate the return char pointer

  for(int i = 0; i < length; i++){   // Write the characters inside "ret"
    ret[i] = str[length - i - 1];
  }
  ret[length] = '\0';                // Terminate "ret" with \0

  return ret;                        // Return the string
}
```
