### Variables
Variables in C must be declared and initialized properly, reference sheet:

```C
// Native Types
char z = 'a';         // 1 Byte  - %c
int x = 0;            // 4 Bytes - %d
float y = 5.25        // 4 Bytes - %f
double z = 1.14159;   // 8 Bytes - %g

// Read-only variables are declared using "const"
const int A = 1;
const double pi = 3.14;

// Variables inside functions are "local", available only inside that function, and allocated on the "stack"
void main(){
  int x = 1;
}

// Convert a variable type to another via "casting"
int num1 = 1;
int num2 = 2;
float ratio = (float)num1 / num2;
```

### TBD (In Progress)
