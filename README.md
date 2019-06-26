# C-Data-Structures
 Contains generic LinkedList, HashTable, Tree, and Iterator data structures (all allocated on the heap) with pretty print features <br>
 
## How to use

All example code in this document will be referring to an arbitrarily created type Person defined in <i>person.h</i> and <i>person.c</i> below:

##### person.h
```c
#include "object.h"

/*
	Some Arbitrary Type "Person":
*/
typedef struct Person {
	char * firstName;
	char * lastName;
	int age;
} Person;

// Person constructor
Person * makePerson (char * firstName, char * lastName, int age);
// Person to string
char * personToString (ObjectType * objectType, void * personPtr);
// Person Compare Function
int personCompareFunction (ObjectType * objectType, void * person1Ptr, void * person2Ptr);
// Person destructor
void personDestructor (ObjectType * objectType, void * personPtr);
```
##### person.c
```c
#include <string.h>
#include <stdlib.h>

#include "person.h"
#include "object.h"
#include "utils.h"


Person * makePerson(char * firstName, char * lastName, int age) {
	Person * p = malloc(sizeof(Person));
	p->firstName = copyString(firstName);
	p->lastName = copyString(lastName);
	p->age = age;
	return p;
}

char * personToString(ObjectType * objectType, void * personPtr) {
	Person p = * (Person *) personPtr;
	int firstNameLength = getStringSize(p.firstName);
	int lastNameLength = getStringSize(p.lastName);
	
	char * ageString = intToString(p.age);
	int ageStringLength = getStringSize(ageString);

	int personStringLength = firstNameLength + lastNameLength + ageStringLength + 4;
	char * personString = malloc(sizeof(char) * (personStringLength + 1) ); 
	
	stringInsert(personString, p.firstName, 0);
	stringInsert(personString, " ", firstNameLength);
	stringInsert(personString, p.lastName, firstNameLength + 1);
	stringInsert(personString, " (", firstNameLength + lastNameLength + 1);
	stringInsert(personString, ageString, firstNameLength + lastNameLength + 3);
	stringInsert(personString, ")", firstNameLength + ageStringLength + lastNameLength +  3);

	personString[personStringLength] = '\0';

	free(ageString);
	return personString;
}

int personCompareFunction (ObjectType * objectType, void * person1Ptr, void * person2Ptr) {
	Person p1 = * (Person *) person1Ptr;
	Person p2 = * (Person *) person2Ptr;
	int firstNameCmp = strcmp(p1.firstName, p2.firstName);
	int lastNameCmp = strcmp(p1.lastName, p2.lastName);
	int ageCmp = p1.age == p2.age;

	return firstNameCmp == 0 && lastNameCmp == 0 && ageCmp == 1;
}

void personDestructor (ObjectType * objectType, void * personPtr) {
	Person p = * (Person *) personPtr;
	free(p.firstName);
	free(p.lastName);
	free(personPtr);
}
```

### ObjectType
 As said above, the data structures contained within this repository are generic. To use any of the data structures, one must specify the type of the data to store within them. This type is specified by creating a ObjectType that is present in "object.h". This ObjectType can be created as a description for any generic type; it contains a function that converts the type to a c-string (<i>ObjectTypeToStringFunction</i>), it contains a function that compares any two instantiations of that type (<i>ObjectTypeCompareFunction</i>), and a destructor function that deallocates the instantiated memory that type (<i>ObjectTypeDestructor</i>). If none of these functions are decided to not be defined for the specified ObjectType, then a null pointer must be passed as an argument when initializing the ObjectType. For example, if <i>ObjectTypeToStringFunction</i> is not defined for a created, then all data structures using this ObjectType will not be printed when printing the whole structure. More on this later.
<br><br>
<b>Note:</b> if the instances of the generic type used are allocated on the stack OR the instances -- when removed from any of the data structures in any way -- are not intended to be freed from the heap, the destructor function (<i>ObjectTypeDestructor</i>) should be null.
<br><br>
To create ObjectType for a generic type, please use the <i>objectTypeInit</i> function which allocates the specified ObjectType structure on the heap. When done using all the data structures using this generic type, one can use the <i>objectTypeDestroy</i> function to deallocate the value on the heap.
<br><br>
Here is an example of making 3 instances of the type "Person" (defined in the section above), then turning each of these instances into their string counterparts. Afterwards, these instances are compared to each other. Finally, each of the instances are destroyed then, the ObjectType of "Person" is destroyed:
<br><br>
##### Example 1
```c
#include <stdio.h>

#include "person.h"
#include "utils.h"
#include "object.h"

int main () {
	ObjectType* personType = objectTypeInit(&personToString, &personCompareFunction, &personDestructor);

	Person * p1 = makePerson("Alan", "Turing", 25);
	Person * p2 = makePerson("Charles", "Babbage", 38);
	Person * p3 = makePerson("Von", "Neumann", 20);

	char * p1ToString = objectTypeToString(personType, p1);
	char * p2ToString = objectTypeToString(personType, p2);
	char * p3ToString = objectTypeToString(personType, p3);

	printf("%s\n\r", p1ToString);
	printf("%s\n\r", p2ToString);
	printf("%s\n\r", p3ToString);

	Person * p1Copy = makePerson("Alan", "Turing", 25);

	int shouldBeFalse = objectTypeCompare(personType, p1, p2);
	int shouldBeTrue = objectTypeCompare(personType, p1, p1Copy);

	printf("%i\n\r", shouldBeFalse);
	printf("%i\n\r", shouldBeTrue);

	objectTypeDestroyValue(personType, p1);
	objectTypeDestroyValue(personType, p2);
	objectTypeDestroyValue(personType, p3);

	objectTypeDestroy(personType);
}
```

##### Output
```
Alan Turing (25)
Charles Babbage (38)
Von Neumann (20)
0
1
```


### LinkedList
 
### HashTable
 
### Tree
 
### Iterator
